# #2663 - websocket: stream_base::pending_guard assertion triggered unexpectedly [Closed]

> Username: weinhold  
> Created at: 2023-03-27 12:01:47 UTC  
> Updated at: 2023-10-28 16:37:20 UTC  
> Closed at: 2023-10-28 16:37:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2663  

### Version of Beast  
  
Beast version: 345  
  
### Steps necessary to reproduce the problem  
  
I'm using Beast to gather data from several exchanges via their websocket API. After a few hours the program runs into the assertion:  
  
```  
/home/user/.conan/data/boost/1.81.0/_/_/package/f92d0579bf71244f71f078480161c8edb4a8a250/include/boost/beast/core/detail/stream_base.hpp:116: void boost::beast::detail::stream_base::pending_guard::assign(bool &): Assertion `! *b_' failed.  
```  
  
The comment at the assertion suggests that the issue is that more than one asynchronous I/O operation of the same type is issued at the same time. However, I'm fairly confident that I'm not doing that. I can't provide the program's full source code, but the part most relevant for the issue is a wrapper class for `beast::websocket::stream`:  
  
```c++  
#include <boost/asio/ssl.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/ssl/ssl_stream.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
  
#include <atomic>  
#include <iostream>  
  
namespace asio = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
namespace websocket = beast::websocket;  
  
namespace net {  
  
    namespace {  
        constexpr std::chrono::seconds kConnectTimeout{30};  
        constexpr std::chrono::seconds kSslHandshakeTimeout{30};  
  
        std::string_view toString(websocket::frame_type frameType) {  
            switch (frameType) {  
                case websocket::frame_type::close:  
                    return "close";  
                case websocket::frame_type::ping:  
                    return "ping";  
                case websocket::frame_type::pong:  
                    return "pong";  
            }  
            return "unknown frame type";  
        }  
  
    } // namespace  
  
    struct WebSocket::Impl : public std::enable_shared_from_this<WebSocket::Impl> {  
        explicit Impl(asio::io_context& ioContext)  
                : mCallbackMutex{},  
                  mErrorCallback{},  
                  mReceiveCallback{},  
                  mSslContext{asio::ssl::context::sslv23_client},  
                  mResolver{asio::make_strand(ioContext)},  
                  mWebSocket{asio::make_strand(ioContext), mSslContext},  
                  mBuffer{},  
                  mHost{},  
                  mPort{},  
                  mTarget{},  
                  mMessage{},  
                  mStatus{Status::CLOSED} {  
            LOG(debug) << "WebSocket " << this << ": constructed";  
        }  
  
        ~Impl() {  
            LOG(debug) << "WebSocket " << this << ": destroying";  
        }  
  
        void setErrorCallback(std::function<void(std::string&)> errorCallback) {  
            std::lock_guard mutexLock{mCallbackMutex};  
            mErrorCallback = std::move(errorCallback);  
        }  
  
        void setReceiveCallback(std::function<void(std::string&)> readCallback) {  
            std::lock_guard mutexLock{mCallbackMutex};  
            mReceiveCallback = std::move(readCallback);  
        }  
  
        void unsetCallbacks() {  
            std::lock_guard mutexLock{mCallbackMutex};  
            mErrorCallback = nullptr;  
            mReceiveCallback = nullptr;  
        }  
  
        void asyncOpen(std::string host, std::string port, std::string target, std::string message) {  
            // Save these for later  
            mHost = std::move(host);  
            mPort = std::move(port);  
            mTarget = std::move(target);  
            mMessage = std::move(message);  
  
            // control callback for debugging only  
            mWebSocket.control_callback([weakSelf = std::weak_ptr(shared_from_this())](auto kind, auto payload) {  
                auto self = weakSelf.lock();  
                if (self != nullptr) {  
                    LOG(debug) << "WebSocket " << self.get() << ": control frame " << self->mHost << ":" << self->mPort  
                               << self->mTarget << ": " << toString(kind) << ": " << payload;  
                }  
            });  
  
            // Look up the domain name  
            mStatus = Status::OPENING;  
            LOG(debug) << "WebSocket " << this << ": resolving " << mHost << ":" << mPort;  
            mResolver.async_resolve(mHost, mPort, beast::bind_front_handler(&Impl::onResolve, shared_from_this()));  
        }  
  
        void asyncClose(std::function<void()> callback) {  
            auto oldStatus = mStatus.load();  
            if (oldStatus == Status::CLOSING || oldStatus == Status::CLOSED)  
                return;  
            mStatus = Status::CLOSING;  
            LOG(debug) << "WebSocket " << this << ": closing websocket " << mHost << ":" << mPort << mTarget;  
            mWebSocket.async_close(  
                    websocket::close_code::normal,  
                    beast::bind_front_handler(&Impl::onClose, shared_from_this(), std::move(callback)));  
        }  
  
        void asyncSend(std::string message) {  
            LOG(debug) << "WebSocket " << this << ": sending message to " << mHost << ":" << mPort + mTarget << ": "  
                       << message;  
            mWebSocket.async_write(  
                    asio::buffer(message), beast::bind_front_handler(&Impl::onWrite, shared_from_this(), false));  
        }  
  
        void asyncSendPing(const std::string& message) {  
            LOG(debug) << "WebSocket " << this << ": sending ping message to " << mHost << ":" << mPort + mTarget  
                       << ": " << message;  
            boost::beast::websocket::ping_data pingData;  
            if (message.size() > pingData.capacity())  
                throw std::invalid_argument("websocket ping payload too large");  
            pingData.assign(message.data(), message.size());  
            mWebSocket.async_ping(pingData, [self = shared_from_this()](const beast::error_code& ec) {  
                if (ec) {  
                    if (!self->isClosedOrClosing() || ec != asio::error::basic_errors::operation_aborted)  
                        self->errorOccurred(  
                                ec,  
                                "failed to send ping to websocket " + self->mHost + ":" + self->mPort + self->mTarget);  
                } else  
                    LOG(debug) << "WebSocket " << self.get() << ": ping message sent successfully";  
            });  
        }  
  
    private:  
        enum class Status {  
            OPENING,  
            OPEN,  
            CLOSING,  
            CLOSED,  
        };  
  
    private:  
        std::mutex mCallbackMutex;  
        std::function<void(std::string&)> mErrorCallback;  
        std::function<void(std::string&)> mReceiveCallback;  
        asio::ssl::context mSslContext;  
        asio::ip::tcp::resolver mResolver;  
        beast::websocket::stream<beast::ssl_stream<beast::tcp_stream>> mWebSocket;  
        beast::flat_buffer mBuffer;  
        std::string mHost;  
        std::string mPort;  
        std::string mTarget;  
        std::string mMessage;  
        std::atomic<Status> mStatus;  
  
    private:  
        void onResolve(beast::error_code ec, const asio::ip::tcp::resolver::results_type& results) {  
            if (ec) {  
                if (!isClosedOrClosing() || ec != asio::error::basic_errors::operation_aborted)  
                    errorOccurred(ec, "failed to resolve " + mHost + ":" + mPort);  
                return;  
            }  
  
            // Set a timeout on the operation  
            beast::get_lowest_layer(mWebSocket).expires_after(kConnectTimeout);  
  
            // Make the connection on the IP address we get from a lookup  
            LOG(debug) << "WebSocket " << this << ": TCP connecting to " << mHost << ":" << mPort;  
            beast::get_lowest_layer(mWebSocket)  
                    .async_connect(results, beast::bind_front_handler(&Impl::onConnect, shared_from_this()));  
        }  
  
        void onConnect(beast::error_code ec, const asio::ip::tcp::resolver::results_type::endpoint_type&) {  
            if (ec) {  
                if (!isClosedOrClosing() || ec != asio::error::basic_errors::operation_aborted)  
                    errorOccurred(ec, "failed to connect to " + mHost + ":" + mPort);  
                return;  
            }  
  
            // Set a timeout on the operation  
            beast::get_lowest_layer(mWebSocket).expires_after(kSslHandshakeTimeout);  
  
            // Set SNI Hostname (many hosts need this to handshake successfully)  
            if (!SSL_set_tlsext_host_name(mWebSocket.next_layer().native_handle(), mHost.c_str())) {  
                ec = beast::error_code(static_cast<int>(::ERR_get_error()), asio::error::get_ssl_category());  
                errorOccurred(ec, "failed to set TLS host name " + mHost);  
                return;  
            }  
  
            // Perform the SSL handshake  
            LOG(debug) << "WebSocket " << this << ": performing SSL handshake with " << mHost << ":" << mPort;  
            mWebSocket.next_layer().async_handshake(  
                    boost::asio::ssl::stream_base::client,  
                    beast::bind_front_handler(&Impl::onSslHandshake, shared_from_this()));  
        }  
  
        void onSslHandshake(beast::error_code ec) {  
            if (ec) {  
                if (!isClosedOrClosing() || ec != asio::error::basic_errors::operation_aborted)  
                    errorOccurred(ec, "SSL handshake to " + mHost + ":" + mPort + " failed");  
                return;  
            }  
  
            // Turn off the timeout on the tcp_stream, because  
            // the websocket stream has its own timeout system.  
            beast::get_lowest_layer(mWebSocket).expires_never();  
  
            // Set suggested timeout settings for the websocket  
            mWebSocket.set_option(websocket::stream_base::timeout::suggested(beast::role_type::client));  
  
            // Set a decorator to change the User-Agent of the handshake  
            mWebSocket.set_option(websocket::stream_base::decorator([](websocket::request_type& req) {  
                req.set(http::field::user_agent, std::string(BOOST_BEAST_VERSION_STRING));  
            }));  
  
            // Perform the websocket handshake  
            LOG(debug) << "WebSocket " << this << ": performing websocket handshake with " << mHost << ":"  
                       << mPort + mTarget;  
            // BitGet doesn't like the host field to contain the (default) port number, so omit it, when it isn't the  
            // default.  
            std::string hostField = mPort == "443" ? mHost : mHost + ":" + mPort;  
            mWebSocket.async_handshake(  
                    hostField, mTarget, beast::bind_front_handler(&Impl::onHandshake, shared_from_this()));  
        }  
  
        void onHandshake(beast::error_code ec) {  
            if (ec) {  
                if (!isClosedOrClosing() || ec != asio::error::basic_errors::operation_aborted)  
                    errorOccurred(ec, "websocket handshake to " + mHost + ":" + mPort + mTarget + " failed");  
                return;  
            }  
  
            Status expectedStatus = Status::OPENING;  
            if (!mStatus.compare_exchange_strong(expectedStatus, Status::OPEN))  
                return;  
  
            // Send the message  
            LOG(debug) << "WebSocket " << this << ": sending request to " << mHost << ":" << mPort + mTarget << ":"  
                       << mMessage;  
            mWebSocket.async_write(  
                    asio::buffer(mMessage), beast::bind_front_handler(&Impl::onWrite, shared_from_this(), true));  
        }  
  
        void onWrite(bool initialRequest, beast::error_code ec, std::size_t bytesTransferred) {  
            boost::ignore_unused(bytesTransferred);  
  
            if (ec) {  
                if (!isClosedOrClosing() || ec != asio::error::basic_errors::operation_aborted)  
                    errorOccurred(ec, "failed to send to websocket " + mHost + ":" + mPort + mTarget);  
                return;  
            }  
  
            // Read a message into our buffer  
            if (initialRequest)  
                receiveFromWebSocket();  
            else  
                LOG(debug) << "WebSocket " << this << ": message sent successfully";  
        }  
  
        void receiveFromWebSocket() {  
            LOG(debug) << "WebSocket " << this << ": receiving from " << mHost << ":" << mPort + mTarget;  
            mWebSocket.async_read(mBuffer, beast::bind_front_handler(&Impl::onRead, shared_from_this()));  
        }  
  
        void onRead(beast::error_code ec, std::size_t /*bytesTransferred*/) {  
            if (mBuffer.size() > 0) {  
                std::lock_guard mutexLock{mCallbackMutex};  
                if (mReceiveCallback != nullptr) {  
                    std::string data = beast::buffers_to_string(mBuffer.cdata());  
                    LOG(debug) << "WebSocket " << this << ": received data from " << mHost << ":" << mPort << mTarget  
                               << ": " << data;  
                    mReceiveCallback(data);  
                }  
            }  
            mBuffer.clear();  
  
            if (ec) {  
                if (!isClosedOrClosing() || ec != asio::error::basic_errors::operation_aborted)  
                    errorOccurred(ec, "failed to receive from websocket " + mHost + ":" + mPort + mTarget);  
                return;  
            }  
  
            receiveFromWebSocket();  
        }  
  
        void onClose(const std::function<void()>& callback, beast::error_code ec) {  
            mStatus = Status::CLOSED;  
            callback();  
            if (ec) {  
                // stream_truncated is OK. The server may just be rude, not shutting down the SSL connection properly.  
                if (ec != asio::ssl::error::stream_truncated)  
                    return errorOccurred(ec, "failed to close websocket " + mHost + ":" + mPort + mTarget);  
            }  
        }  
  
        bool isClosedOrClosing() const {  
            return mStatus == Status::CLOSED || mStatus == Status::CLOSING;  
        }  
  
        void errorOccurred(beast::error_code ec, std::string what) {  
            LOG(error) << "WebSocket " << this << ": " << what << ": " << ec.message();  
            std::lock_guard mutexLock{mCallbackMutex};  
            if (mErrorCallback != nullptr) {  
                std::string error = what + (": " + ec.to_string());  
                mErrorCallback(error);  
            }  
        }  
    };  
} // namespace net  
```  
  
The code writes a log entry before issuing any asynchronous operation, as well as in the corresponding completion handler. That's why it is fairly easy to verify that the program does indeed not issue any concurrent operation of the same type at the same time. Even filtered by the particular websocket, the log is > 1GiB, so here's just an excerpt to show what's going on:  
  
```  
2023-03-26 17:30:02.435692 debug    net                  - WebSocket 0x558a4b3dc590: constructed  
2023-03-26 17:30:02.435844 debug    net                  - WebSocket 0x558a4b3dc590: resolving stream.bybit.com:443  
2023-03-26 17:30:02.692817 debug    net                  - WebSocket 0x558a4b3dc590: TCP connecting to stream.bybit.com:443  
2023-03-26 17:30:02.717228 debug    net                  - WebSocket 0x558a4b3dc590: performing SSL handshake with stream.bybit.com:443  
2023-03-26 17:30:02.744964 debug    net                  - WebSocket 0x558a4b3dc590: performing websocket handshake with stream.bybit.com:443/v5/public/linear  
2023-03-26 17:30:03.295017 debug    net                  - WebSocket 0x558a4b3dc590: sending request to stream.bybit.com:443/v5/public/linear:{"args": ...}  
2023-03-26 17:30:03.295560 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
2023-03-26 17:30:03.486340 debug    net                  - WebSocket 0x558a4b3dc590: received data from stream.bybit.com:443/v5/public/linear: {"topic":"orderbook.50.BTCUSDT", ...}  
2023-03-26 17:30:03.490111 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
2023-03-26 17:30:03.490273 debug    net                  - WebSocket 0x558a4b3dc590: received data from stream.bybit.com:443/v5/public/linear: {"topic":"orderbook.50.BTCUSDT", ...}  
2023-03-26 17:30:03.490692 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
...  
2023-03-26 17:30:22.434925 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
2023-03-26 17:30:22.435958 debug    net                  - WebSocket 0x558a4b3dc590: received data from stream.bybit.com:443/v5/public/linear: {"topic":"orderbook.50.XRPUSDT", ...}  
2023-03-26 17:30:22.436021 debug    net                  - WebSocket 0x558a4b3dc590: sending message to stream.bybit.com:443/v5/public/linear: {"op":"ping"}  
2023-03-26 17:30:22.436648 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
2023-03-26 17:30:22.436831 debug    net                  - WebSocket 0x558a4b3dc590: message sent successfully  
2023-03-26 17:30:22.437452 debug    net                  - WebSocket 0x558a4b3dc590: received data from stream.bybit.com:443/v5/public/linear: {"topic":"orderbook.50.MATICUSDT", ...}  
2023-03-26 17:30:22.437793 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
...  
2023-03-26 21:58:43.709278 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
2023-03-26 21:58:43.712061 debug    net                  - WebSocket 0x558a4b3dc590: sending message to stream.bybit.com:443/v5/public/linear: {"op":"ping"}  
2023-03-26 21:58:43.712847 debug    net                  - WebSocket 0x558a4b3dc590: message sent successfully  
2023-03-26 21:58:43.721524 debug    net                  - WebSocket 0x558a4b3dc590: received data from stream.bybit.com:443/v5/public/linear: {"topic":"orderbook.50.DOTUSDT", ...}  
2023-03-26 21:58:43.721952 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
...  
2023-03-26 21:59:03.712319 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
2023-03-26 21:59:03.712566 debug    net                  - WebSocket 0x558a4b3dc590: received data from stream.bybit.com:443/v5/public/linear: {"topic":"orderbook.50.SOLUSDT", ...}  
2023-03-26 21:59:03.712965 debug    net                  - WebSocket 0x558a4b3dc590: sending message to stream.bybit.com:443/v5/public/linear: {"op":"ping"}  
2023-03-26 21:59:03.713028 debug    net                  - WebSocket 0x558a4b3dc590: receiving from stream.bybit.com:443/v5/public/linear  
```  
  
After initiating the connection and sending a subscription request, the program keeps receiving data forever (i.e. until the websocket would be closed). Every 20 seconds, an application-level ping message is sent. The tail of the log is shown: Sending such a ping message is requested, but the completion handler has not been invoked yet -- the one 20 seconds earlier has completed, though. Inspecting the core dump, I can see that the operation for which the assertion is triggered is a write operation and the completion handler refers to the `WebSocket::Impl::onWrite()` method. So, there's apparently already another write operation, but it has not been issued by my code.  
  
As per documentation, an automatically handled pong response to a ping should be safe, but just to be sure I added a control callback. It is never invoked, though.  
  
In the `transfer_op` constructor in basic_stream.hpp, I noticed an explicit work-around for openssl behavior (branch not taking in my case). I wonder if my issue could be something similar, i.e. openssl issuing an internal write in response to a read.  
  
I'll gladly dig in the core dump, if you have specific suggestions what to look for.  
  
### All relevant compiler information  
  
Built with clang 15.0.7 -std=c++20

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-03-27 12:21:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2663#issuecomment-1485043782  

Can't the `receiveFromWebSocket` be invoked from onRead and onWrite so that it overlapped?

---

## Comment 2

> Username: weinhold  
> Created at: 2023-03-27 12:29:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2663#issuecomment-1485049383  

`onWrite()` calls `receiveFromWebSocket()` only for the initial request right after the handshake, in order to get the infinite read "loop" going. `asyncSend()` passes a completion callback that calls `onWrite()` with `initialRequest = false`.

---

## Comment 3

> Username: weinhold  
> Created at: 2023-04-20 08:16:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2663#issuecomment-1515916010  

TL;DR: `beast::websocket::stream::async_read()` is actually triggering a write operation.  
  
Not being aware of a better way to approach this issue, I patched "beast/include/boost/beast/core/detail/stream_base.hpp" to collect some more information that I can easily inspect when looking at the core dump with gdb. The patch is attached ([1.81.0-debug.patch.txt](https://github.com/boostorg/beast/files/11283939/1.81.0-debug.patch.txt)). It replaces the `bool basic_op_state::pending` with a struct `pending_state` that also contains a buffer for some debug information. I wrote the home-grown `SimpleStringStream` because `std::stringstream` turned out to be very inconvenient to inspect. In `pending_guard` some details (including a backtrace) are recorded when the `pending_guard` is constructed with, moved from, or assigned a `pending_state`. So, this allows to see the recent history of the type of operation for the stream when hitting the assertion. It is attached (I enriched the backtraces with symbol/source location information -- [2023-04-16-debug-beast-assertion.txt](https://github.com/boostorg/beast/files/11283947/2023-04-16-debug-beast-assertion.txt)).  
  
After spotting nothing obviously problematic therein -- a `pending_guard` is initialized for the write operation in response to the `WebSocket::Impl::asyncSend()` and moved a few times -- I looked closer at the backtrace of the thread hitting the assertion ([2023-04-16-backtrace.txt](https://github.com/boostorg/beast/files/11283948/2023-04-16-backtrace.txt)), which I should have done earlier: The `WebSocket::Impl::receiveFromWebSocket()` which calls `beast::websocket::stream::async_read()` (stack frames 31/30) ends up triggering a write operation in the SSL layer (cf. frame 15).

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-04-24 02:25:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2663#issuecomment-1519293076  

Does that mean you figured it out? Because it sounds like you're callign async_read before the previous operation completed.

---

## Comment 5

> Username: weinhold  
> Created at: 2023-04-24 07:55:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2663#issuecomment-1519565721  

I'm calling `beast::websocket::stream::async_read()` before the operation triggered by an earlier `beast::websocket::stream::async_write()` has completed. As I understand the documentation, as long as each of the operations are of different type (read, write, ping/pong, close), multiple ones can be pending at the same time. If that is correct, my use of the API should be perfectly legal, and I would consider beast's behavior a bug.  
  
The issue seems to be that at the SSL layer the read operation somehow results in a write operation on the underlying layer. If I had to guess, I'd say that the cause is something like SSL renegotiation initiated by the server: The client wants to read, but has to perform a write to complete the renegotiation.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-05-15 01:43:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2663#issuecomment-1547077569  

This is odd for sure. Are you running on multiple threads without a strand?

---

## Comment 7

> Username: ashtum  
> Created at: 2023-08-19 05:12:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2663#issuecomment-1684822895  

@weinhold Is this still open?

---

## Comment 8

> Username: weinhold  
> Created at: 2023-08-21 10:20:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2663#issuecomment-1686054890  

I have analyzed the issue as far as I could without attempting to become a beast/asio expert. My full analysis is above. The short of it is, that a client read operation seems to trigger a write operation at the SSL layer which then conflicts with a client write operation. I used the code to collect real-time data from five different exchanges and only ever saw the issue with one of them, which suggests that specific server-side behavior is triggering the issue. My suspicion is that the server requests SSL renegotiation, which the client would notice during a read operation and issue a write in response. I was hoping someone familiar with the code base could check whether something like this could happen.  
  
I have implemented a work-around in my class: regardless of their type, all operations are put in a queue and an operation is not executed before the previous one has finished. This way, a read operation could starve a queued write operation, but since the servers are pushing data with high frequency, this is not an issue in my use case. With the work-around, I'm no longer seeing websocket related issues in my application.  
  
TL;DR: I have found a work-around and won't investigate the issue any further. Feel free to check whether my suspicion may be correct, or don't if you don't think it's worth it.
