# #2761 - websocket async write will cause data to be written incorrectly [Closed]

> Username: rmGFW  
> Created at: 2023-10-28 02:12:44 UTC  
> Updated at: 2023-10-29 09:38:32 UTC  
> Closed at: 2023-10-29 09:38:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2761  

here is the code , i use a queue to buffer write operation , and write it to websocket sequential, but this impl will cause payload changed on transmitting ,  and i found the changed part is exactly 8-bytes   
<img width="1205" alt="image" src="https://github.com/boostorg/boost/assets/66858140/3f43e8a7-ce6a-4d97-95ad-1935e51a8206">  
  
```c++  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <cstdlib>  
#include <fstream>  
#include <iostream>  
#include <memory>  
#include <mutex>  
#include <queue>  
#include <string>  
#include <unordered_map>  
  
namespace beast = boost::beast;  
namespace http = beast::http;  
namespace websocket = beast::websocket;  
namespace net = boost::asio;  
using tcp = boost::asio::ip::tcp;  
  
std::unordered_map<uint64_t, std::shared_ptr<tcp::socket>> sessionIdToSocketMap;  
  
class Session : public std::enable_shared_from_this<Session> {  
   private:  
    websocket::stream<tcp::socket> ws_;  
    net::io_context& ioc_;  
    std::queue<beast::flat_buffer> ws_write_buffer_;  
    std::queue<std::pair<uint64_t, std::shared_ptr<beast::flat_buffer>>>  
        tcp_write_buffer_;  
  
   private:  
    bool writing_to_ws_ = false;  
  
    void create_conn(uint64_t session_id) {  
        auto socket = std::make_shared<tcp::socket>(ioc_);  
        socket->connect(  
            tcp::endpoint(net::ip::make_address("127.0.0.1"), 8000));  
        sessionIdToSocketMap[session_id] = socket;  
  
        net::post(this->ioc_,  
                  [self = shared_from_this(), session_id, socket]() {  
                      self->do_tcp_read(session_id, socket);  
                  });  
    }  
  
    void do_ws_write() {  
        if (writing_to_ws_ || ws_write_buffer_.empty()) {  
            return;  
        }  
        writing_to_ws_ = true;  
        beast::flat_buffer buffer = ws_write_buffer_.front();  
        ws_.binary(true);  
        ws_.async_write(  
            buffer.data(), [this, buffer](boost::system::error_code ec,  
                                          std::size_t size_written) {  
                if (!ec) {  
                    ws_write_buffer_.pop();  
                    std::printf("%ld size written\n", size_written);  
                    writing_to_ws_ = false;  
                    if (!ws_write_buffer_.empty()) {  
                        net::post(this->ioc_, [this]() { do_ws_write(); });  
                    }  
                } else {  
                    std::cout << "❌ write websocket failed:" << ec.message()  
                              << std::endl;  
                }  
            });  
    }  
  
    void do_tcp_read(uint64_t sessionId, std::shared_ptr<tcp::socket> socket) {  
        auto buffer = std::make_shared<beast::flat_buffer>();  
        socket->async_read_some(  
            buffer->prepare(8192),  
            [self = shared_from_this(), sessionId, buffer, socket](  
                boost::system::error_code ec, std::size_t bytes_read) {  
                if (!ec) {  
                    buffer->commit(bytes_read);  
                    std::printf("session id %llu , %lu bytes read\n", sessionId,  
                                bytes_read);  
                    uint64_t big_endian_session_id = htonll(sessionId);  
                    beast::flat_buffer flat_buffer;  
                    flat_buffer.reserve(8 + bytes_read);  
                    flat_buffer.commit(net::buffer_copy(  
                        flat_buffer.prepare(8),  
                        net::buffer(&big_endian_session_id, sizeof(uint64_t))));  
                    flat_buffer.commit(  
                        net::buffer_copy(flat_buffer.prepare(bytes_read),  
                                         buffer->data(), bytes_read));  
  
  
                     
                    self->ws_write_buffer_.push(std::move(flat_buffer));  
                    self->do_ws_write();  
  
                    // ❗️ turn off the comments to enable sync write (this version is no problem)  
                   // self->ws_.binary(true);  
                   // self->ws_.write(flat_buffer.data());  
  
                    net::post(self->ioc_, [self, sessionId, socket]() {  
                        self->do_tcp_read(sessionId, socket);  
                    });  
                } else {  
                    sessionIdToSocketMap.erase(sessionId);  
                    socket->close();  
                    std::cout << "❌ read tcp failed:" << ec.message()  
                              << std::endl;  
                }  
            });  
    }  
  
    void do_tcp_write() {  
        if (tcp_write_buffer_.empty())  
            return;  
  
        auto [session_id, buffer] = tcp_write_buffer_.front();  
        auto socket = sessionIdToSocketMap[session_id];  
  
        net::async_write(  
            *socket, buffer->data(),  
            [self = shared_from_this()](boost::system::error_code write_ec,  
                                        std::size_t bytes_written) {  
                if (write_ec) {  
                    std::cerr << "❌ Async write error to TCP socket: "  
                              << write_ec.message() << "\n";  
                    return;  
                }  
  
                self->tcp_write_buffer_.pop();  
                if (!self->tcp_write_buffer_.empty()) {  
                    net::post(self->ioc_, [self]() { self->do_tcp_write(); });  
                }  
            });  
    }  
  
    void do_ws_read() {  
        auto buffer = std::make_shared<beast::flat_buffer>();  
        ws_.got_binary();  
        ws_.async_read(  
            *buffer, [self = shared_from_this(), buffer](  
                         beast::error_code ec, std::size_t bytes_transferred) {  
                if (ec) {  
                    std::cerr << "❌ Read error: " << ec.message() << "\n";  
                    return;  
                }  
  
                const char* buf_ptr =  
                    boost::asio::buffer_cast<const char*>(buffer->data());  
  
                uint64_t session_id = 0;  
                std::memcpy(&session_id, buf_ptr, 8);  
  
                session_id = ntohll(session_id);  
                buffer->consume(8);  
  
                if (sessionIdToSocketMap.find(session_id) ==  
                    sessionIdToSocketMap.end()) {  
                    self->create_conn(session_id);  
                }  
  
                auto socket = sessionIdToSocketMap[session_id];  
                self->tcp_write_buffer_.emplace(session_id, buffer);  
                if (self->tcp_write_buffer_.size() == 1) {  
                    self->do_tcp_write();  
                }  
                net::post(self->ioc_, [self]() { self->do_ws_read(); });  
            });  
    }  
  
   public:  
    explicit Session(net::io_context& ioc) : ws_(ioc), ioc_(ioc) {}  
  
    void start(const std::string& host, const std::string& port) {  
        tcp::resolver resolver{ws_.get_executor()};  
        auto const results = resolver.resolve(host, port);  
        auto ep = net::connect(ws_.next_layer(), results);  
        auto target = host + ':' + std::to_string(ep.port());  
  
        ws_.set_option(  
            websocket::stream_base::decorator([](websocket::request_type& req) {  
                req.set(http::field::user_agent,  
                        std::string(BOOST_BEAST_VERSION_STRING) +  
                            " websocket-client-coro");  
            }));  
  
        ws_.async_handshake(  
            target, "/wait-mux-attach",  
            [self = shared_from_this()](beast::error_code ec) {  
                if (ec) {  
                    std::cerr << "❌ Handshake error: " << ec.message() << "\n";  
                    return;  
                } else {  
                    std::cout << "✅ Handshake success\n";  
                }  
                self->do_ws_read();  
            });  
    }  
};  
  
int main() {  
    try {  
        std::string host = "localhost";  
        auto const port = "1997";  
  
        net::io_context ioc;  
  
        auto session = std::make_shared<Session>(ioc);  
        session->start(host, port);  
  
        ioc.run();  
    } catch (const std::exception& e) {  
        std::cerr << "Exception: " << e.what() << "\n";  
    }  
}  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2023-10-28 06:57:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1783726426  

I believe the problem is that you are making a copy of the buffer on the stack and then trying to initiate the async operation with it. Instead, try using a reference to that element:  
  
```C++  
beast::flat_buffer& buffer = ws_write_buffer_.front();  
ws_.binary(true);  
ws_.async_write(....  
```  
  
Side note:  
You've used resolve and connect in your code, which are not asynchronous. To make your code fully asynchronous, you might want to use the asynchronous versions of these functions.

---

## Comment 2

> Username: rmGFW  
> Created at: 2023-10-28 12:37:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1783797781  

> I believe the problem is that you are making a copy of the buffer on the stack and then trying to initiate the async operation with it. Instead, try using a reference to that element:  
>   
> ```c++  
> beast::flat_buffer& buffer = ws_write_buffer_.front();  
> ws_.binary(true);  
> ws_.async_write(....  
> ```  
>   
> Side note: You've used resolve and connect in your code, which are not asynchronous. To make your code fully asynchronous, you might want to use the asynchronous versions of these functions.  
  
thanks!

---

## Comment 3

> Username: rmGFW  
> Created at: 2023-10-28 17:47:44 UTC  
> Updated at: 2023-10-28 17:49:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1783882332  

> I believe the problem is that you are making a copy of the buffer on the stack and then trying to initiate the async operation with it. Instead, try using a reference to that element:  
>   
> ```c++  
> beast::flat_buffer& buffer = ws_write_buffer_.front();  
> ws_.binary(true);  
> ws_.async_write(....  
> ```  
>   
> Side note: You've used resolve and connect in your code, which are not asynchronous. To make your code fully asynchronous, you might want to use the asynchronous versions of these functions.  
  
I found that this function also has data errors. I looked for it according to your idea, but I didn't find the problem. Can you help me take a look? Thanks for your hard work.  
<img width="416" alt="image" src="https://github.com/boostorg/beast/assets/66858140/a5640c0e-3af9-4550-823d-ac9de63f3e3b">  
  
```c++  
    void do_ws_read() {  
        auto buffer = std::make_shared<beast::flat_buffer>();  
  
        ws_.async_read(  
            *buffer, [self = shared_from_this(), buffer](  
                         beast::error_code ec, std::size_t bytes_transferred) {  
                if (ec) {  
                    std::cerr << "❌ Read error: " << ec.message() << "\n";  
                    return;  
                }  
  
                const char* buf_ptr =  
                    boost::asio::buffer_cast<const char*>(buffer->data());  
  
                uint64_t session_id = 0;  
                std::memcpy(&session_id, buf_ptr, 8);  
  
                session_id = ntohll(session_id);  
                buffer->consume(8);  
  
                if (sessionIdToSocketMap.find(session_id) ==  
                    sessionIdToSocketMap.end()) {  
                    self->create_conn(session_id);  
                }  
  
                self->tcp_write_buffer_.emplace(session_id, buffer);  
                std::printf("buffer content\n%s\n", buffer->data());  
                if (!self->writing_tcp_[session_id]) {  
                    self->do_tcp_write();  
                }  
                net::post(self->ioc_, [self]() { self->do_ws_read(); });  
            });  
    }  
```

---

## Comment 4

> Username: ashtum  
> Created at: 2023-10-28 18:07:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1783887051  

Are those HTTP headers part of the message?  
Could you check with Wireshark what is the actual message received in your side?

---

## Comment 5

> Username: rmGFW  
> Created at: 2023-10-28 18:38:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1783893882  

> Are those HTTP headers part of the message?  
> Could you check with Wireshark what is the actual message received in your side?  
  
i already check it in server side, the header is the actually message but the random string not

---

## Comment 6

> Username: ashtum  
> Created at: 2023-10-29 05:45:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1784006000  

If the headers are always intact, there is a good possibility that the rest of the message is fine as well. This might be an encrypted message or possibly a Unicode issue. It would be helpful if you verify it by Wireshark.

---

## Comment 7

> Username: rmGFW  
> Created at: 2023-10-29 06:50:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1784015090  

> If the headers are always intact, there is a good possibility that the rest of the message is fine as well. This might be an encrypted message or possibly a Unicode issue. It would be helpful if you verify it by Wireshark.  
  
<img width="851" alt="image" src="https://github.com/boostorg/beast/assets/66858140/5b4b4954-bcb8-48be-a06f-0c566e5549bb">  
<img width="650" alt="image" src="https://github.com/boostorg/beast/assets/66858140/2850128f-ae10-49ba-8b32-6aa0cfa4592b">  
<img width="584" alt="image" src="https://github.com/boostorg/beast/assets/66858140/cc1cd6bd-cf00-4ccf-9580-5b2f050ee305">  
  
i already verify it by Wireshark , and payload do not have random string , but the local buffer have

---

## Comment 8

> Username: ashtum  
> Created at: 2023-10-29 07:09:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1784018376  

> <img alt="image" width="584" src="https://user-images.githubusercontent.com/66858140/278855396-cc1cd6bd-cf00-4ccf-9580-5b2f050ee305.png">  
> i already verify it by Wireshark , and payload do not have random string , but the local buffer have  
  
Is this what you see in the console? It doesn't look like a random string. By the way, you might want to try using the synchronous versions of the read and write functions if you want to debug more quickly and reduce the potential for bugs.

---

## Comment 9

> Username: rmGFW  
> Created at: 2023-10-29 07:13:26 UTC  
> Updated at: 2023-10-29 07:18:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1784019053  

> > <img alt="image" width="584" src="https://user-images.githubusercontent.com/66858140/278855396-cc1cd6bd-cf00-4ccf-9580-5b2f050ee305.png">  
> > i already verify it by Wireshark , and payload do not have random string , but the local buffer have  
>   
> Is this what you see in the console? It doesn't look like a random string. By the way, you might want to try using the synchronous versions of the read and write functions if you want to debug more quickly and reduce the potential for bugs.  
  
sometimes looks like random string ,but this time seems last response content .   
you can see in the picture , bytes_transferred  size s is 86, but i print it more than 86 bytes   
can beast websocket read write at same time ?

---

## Comment 10

> Username: rmGFW  
> Created at: 2023-10-29 09:38:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2761#issuecomment-1784048575  

@ashtum thanks ashtum, finally,  i find all bugs (some is in the server side)
