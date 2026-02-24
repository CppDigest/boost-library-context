# #2249 - User defined tear down with boost:wintls [Closed]

> Username: gitgitwhat  
> Created at: 2021-05-29 14:13:47 UTC  
> Updated at: 2021-06-01 02:49:03 UTC  
> Closed at: 2021-06-01 02:49:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2249  

I'm looking for help implementing the user defined tear down for boost:wintls (https://github.com/laudrup/boost-wintls).  I'm a C developer so C++, and Boost in particular, are new to me so apologies if this is a dumb question.  
  
Following the documentation here (https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/teardown.html) I tried the following.  
  
    template<class NextLayer>  
    class wintls_stream : public boost::wintls::stream {  
        template<class SyncStream>  
        friend  
            void  
            teardown(  
                boost::beast::role_type role,  
                wintls_stream<SyncStream>& stream,  
                boost::system::error_code& ec);  
  
        template<class AsyncStream, class TeardownHandler>  
        friend  
            void  
            async_teardown(  
                boost::beast::role_type role,  
                wintls_stream<AsyncStream>& stream,  
                TeardownHandler&& handler);  
    };  
  
    template<class SyncStream>  
    static inline void teardown(boost::beast::role_type role, wintls_stream<SyncStream>& stream, boost::system::error_code& ec) {  
        // Just forward it to the underlying ssl::stream  
        using boost::beast::websocket::teardown;  
        teardown(role, *stream.p_, ec);  
    }  
  
    template<class AsyncStream, class TeardownHandler>  
    static inline void async_teardown(boost::beast::role_type role, wintls_stream<AsyncStream>& stream, TeardownHandler&& handler) {  
        // Just forward it to the underlying ssl::stream  
        using boost::beast::websocket::async_teardown;  
        async_teardown(role, *stream.p_, std::forward<TeardownHandler>(handler));  
    }  
  
However I get the these compiling errors.  
  
    WebSockets.cpp(27,1): error C2955: 'boost::wintls::stream': use of class template requires template argument list  
    boost\wintls\stream.hpp(45): message : see declaration of 'boost::wintls::stream'  
    WebSockets.cpp(46): message : see reference to class template instantiation 'wintls_stream<NextLayer>' being compiled  
  
Any help would be appreciated.  Thanks.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-29 17:48:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2249#issuecomment-850872077  

Your instincts are mostly correct.  
  
```  
template<class NextLayer>  
class wintls_stream : public boost::wintls::stream  
```  
Looks like the problem. Should it be:  
```  
template<class NextLayer>  
class wintls_stream : public boost::wintls::stream<NextLayer>  
```  
?  
  
The overloads of async_teardown and teardown should not be static or inline. Template functions are "inline" by nature. Giving template functions static linkage makes no sense.

---

## Comment 2

> Username: gitgitwhat  
> Created at: 2021-05-29 18:53:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2249#issuecomment-850882442  

Thanks.  That did solve that problem but a new one pops up.  
  
    \boost\beast\websocket\teardown.hpp(55,33): error C2338: Unknown Socket type in teardown.  
    \boost\beast\websocket\impl\stream.hpp(331): message : see reference to function template instantiation 'void boost::beast::websocket::teardown<NextLayer>(boost::beast::role_type,Socket &,boost::beast::error_code &)' being compiled  
            with  
            [  
                NextLayer=boost::wintls::stream<boost::asio::ip::tcp::socket>,  
                Socket=boost::wintls::stream<boost::asio::ip::tcp::socket>  
            ]  
  
This is the original error I've been fighting with in trying to get boost::wintls working with beast.  Instead of trying to write my own handlers, I took the ones provided in <boost/beast/websocket/ssl.hpp> and simply replaced net::ssl with boost::wintls.  But this is still throwing the 'unknown socket type' compile error.  
  
  
    template<class SyncStream>  
    void teardown(boost::beast::role_type role, boost::wintls::stream<SyncStream>& stream, boost::system::error_code& ec);  
  
    template<class AsyncStream>  
    void teardown(boost::beast::role_type role, boost::wintls::stream<AsyncStream>& stream, boost::system::error_code& ec) {  
        stream.shutdown(ec);  
        using boost::beast::websocket::teardown;  
        boost::system::error_code ec2;  
        teardown(role, stream.next_layer(), ec ? ec2 : ec);  
    }  
  
    template<class AsyncStream, class TeardownHandler>  
    void async_teardown(boost::beast::role_type role, boost::wintls::stream<AsyncStream>& stream, TeardownHandler&& handler);  
  
    namespace wintls_async_td_detail {  
        template<class AsyncStream>  
        struct wintls_shutdown_op : boost::asio::coroutine {  
            wintls_shutdown_op(boost::wintls::stream<AsyncStream>& s, boost::beast::role_type role) : s_(s) , role_(role) {  
            }  
  
            template<class Self>  
            void operator()(Self& self, boost::system::error_code ec = {}, std::size_t = 0) {  
                BOOST_ASIO_CORO_REENTER(*this) {  
                    BOOST_ASIO_CORO_YIELD  
                    s_.async_shutdown(std::move(self));  
                    ec_ = ec;  
  
                    using boost::beast::websocket::async_teardown;  
                    BOOST_ASIO_CORO_YIELD  
                    async_teardown(role_, s_.next_layer(), std::move(self));  
                    if (!ec_) {  
                        ec_ = ec;  
                    }  
  
                    self.complete(ec_);  
                }  
            }  
  
        private:  
            boost::wintls::stream::stream<AsyncStream>& s_;  
            boost::beast::role_type role_;  
            boost::system::error_code ec_;  
        };  
    }  
  
    template<class AsyncStream, class TeardownHandler>  
        void async_teardown(boost::beast::role_type role, boost::wintls::stream<AsyncStream>& stream, TeardownHandler&& handler) {  
        return boost::asio::async_compose<TeardownHandler, void(boost::system::error_code)>(  
            wintls_async_td_detail::wintls_shutdown_op<AsyncStream>(stream, role), handler, stream);  
    }

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-29 19:33:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2249#issuecomment-850887861  

difficult to comment without having a project to debug. Is there any way you can get a minimal reproducible example to me, either on github or via a simple git repo (which pulls in all dependencies)?  
  
You might need to put the async_shutdown specialisation in the wintls namespace as I believe async_shutdown relies on ADL.

---

## Comment 4

> Username: gitgitwhat  
> Created at: 2021-05-29 20:45:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2249#issuecomment-850896267  

Here is the full code for a simple example of trying to make beast work with wintls.  Besides the teardown error, this fails during handshake (I can comment out the compile assert to at least get it running).  Not entirely sure why the handshake is failing yet either.  I can see the successful cert exchange in Wireshark so it is at least getting that far.  
  
I essentially mashed together this beast example (https://github.com/boostorg/beast/blob/master/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp) with this wintls example (https://laudrup.github.io/boost-wintls/#handshake-type).  
  
wintls is headers only and can be found here https://github.com/laudrup/boost-wintls  
  
```  
#include <iostream>  
#include <stdio.h>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/wintls/wintls.hpp>  
  
namespace beast = boost::beast;      // from <boost/beast.hpp>  
namespace http = beast::http;        // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;         // from <boost/asio.hpp>  
namespace ssl = boost::wintls;       // from <boost/wintls/wintls.hpp>  
  
using tcp = boost::asio::ip::tcp;    // from <boost/asio/ip/tcp.hpp>  
  
template<class SyncStream>  
void teardown(boost::beast::role_type role, boost::wintls::stream<SyncStream>& stream, boost::system::error_code& ec);  
  
template<class AsyncStream>  
void teardown(boost::beast::role_type role, boost::wintls::stream<AsyncStream>& stream, boost::system::error_code& ec) {  
    stream.shutdown(ec);  
    using boost::beast::websocket::teardown;  
    boost::system::error_code ec2;  
    teardown(role, stream.next_layer(), ec ? ec2 : ec);  
}  
  
template<class AsyncStream, class TeardownHandler>  
void async_teardown(boost::beast::role_type role, boost::wintls::stream<AsyncStream>& stream, TeardownHandler&& handler);  
  
namespace wintls_async_td_detail {  
    template<class AsyncStream>  
    struct wintls_shutdown_op : boost::asio::coroutine {  
        wintls_shutdown_op(boost::wintls::stream<AsyncStream>& s, boost::beast::role_type role) : s_(s), role_(role) {  
        }  
  
        template<class Self>  
        void operator()(Self& self, boost::system::error_code ec = {}, std::size_t = 0) {  
            BOOST_ASIO_CORO_REENTER(*this) {  
                BOOST_ASIO_CORO_YIELD  
                    s_.async_shutdown(std::move(self));  
                ec_ = ec;  
  
                using boost::beast::websocket::async_teardown;  
                BOOST_ASIO_CORO_YIELD  
                    async_teardown(role_, s_.next_layer(), std::move(self));  
                if (!ec_) {  
                    ec_ = ec;  
                }  
  
                self.complete(ec_);  
            }  
        }  
  
    private:  
        boost::wintls::stream::stream<AsyncStream>& s_;  
        boost::beast::role_type role_;  
        boost::system::error_code ec_;  
    };  
}  
  
template<class AsyncStream, class TeardownHandler>  
void async_teardown(boost::beast::role_type role, boost::wintls::stream<AsyncStream>& stream, TeardownHandler&& handler) {  
    return boost::asio::async_compose<TeardownHandler, void(boost::system::error_code)>(  
        wintls_async_td_detail::wintls_shutdown_op<AsyncStream>(stream, role), handler, stream);  
}  
  
bool testwebsocket() {  
    bool ret = false;  
    std::string host = "echo.websocket.org";  
    auto const port = "443";  
    auto const text = "test123";  
  
    try {  
        // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // The SSL context is required, and holds certificates  
        //ssl::context ctx{ ssl::context::tlsv12_client };  
        ssl::context ctx{ boost::wintls::method::system_default };  
        ctx.set_default_verify_paths();  
        ctx.verify_server_certificate(true);  
  
        // This holds the root certificate used for verification  
        //load_root_certificates(ctx);  
        //ssl::stream<tcp::socket> stream(ioc, ctx);  
  
        // These objects perform our I/O  
        tcp::resolver resolver{ ioc };  
        websocket::stream<ssl::stream<tcp::socket>> ws{ ioc, ctx };  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        auto ep = net::connect(beast::get_lowest_layer(ws), results);  
  
        ws.next_layer().set_server_hostname(host);  
  
        //// Set SNI Hostname (many hosts need this to handshake successfully)  
        //if (!SSL_set_tlsext_host_name(ws.next_layer().native_handle(), host.c_str())) {  
        //    throw beast::system_error(  
        //        beast::error_code(  
        //            static_cast<int>(::ERR_get_error()),  
        //            net::error::get_ssl_category()),  
        //        "Failed to set SNI Hostname");  
        //}  
  
        // Update the host_ string. This will provide the value of the  
        // Host HTTP header during the WebSocket handshake.  
        // See https://tools.ietf.org/html/rfc7230#section-5.4  
        host += ':' + std::to_string(ep.port());  
  
        // Perform the SSL handshake  
        ws.next_layer().handshake(boost::wintls::handshake_type::client);  
  
        // Set a decorator to change the User-Agent of the handshake  
        ws.set_option(websocket::stream_base::decorator(  
            [](websocket::request_type& req) {  
                req.set(http::field::user_agent,  
                    std::string(BOOST_BEAST_VERSION_STRING) +  
                    " websocket-client-coro");  
            }));  
  
        // Perform the websocket handshake  
        ws.handshake(host, "/");  
  
        // Send the message  
        ws.write(net::buffer(std::string(text)));  
  
        // This buffer will hold the incoming message  
        beast::flat_buffer buffer;  
  
        // Read a message into our buffer  
        ws.read(buffer);  
  
        // Close the WebSocket connection  
        ws.close(websocket::close_code::normal);  
        //ws.next_layer().shutdown();  
  
        // If we get here then the connection is closed gracefully  
  
        // The make_printable() function helps print a ConstBufferSequence  
        std::cout << beast::make_printable(buffer.data()) << std::endl;  
  
        ret = true;  
    } catch (std::exception const& e) {  
        std::cerr << "Error: " << e.what() << std::endl;  
    }  
  
    return ret;  
}  
  
int main(int argc, char** argv) {  
    testwebsocket();  
}  
```

---

## Comment 5

> Username: gitgitwhat  
> Created at: 2021-05-30 23:50:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2249#issuecomment-851081518  

So, the above teardown code will compile once wrapped in boost and beast namespaces.  
  
```  
namespace boost {  
    namespace beast {  
```  
  
I haven't been able to test the actual implementation but getting past this compile error seems like a win.  Definitely appreciate your help trying to solve it.
