# #2933 - How to receive 100-continue with async API [Closed]

> Username: xiaoma565  
> Created at: 2024-09-23 17:07:53 UTC  
> Updated at: 2024-09-25 08:31:43 UTC  
> Closed at: 2024-09-25 08:31:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2933  

I readed example https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/more_examples/expect_100_continue_server.html with how to receive 100-continue message.  
But my code was call by async API.  
My code is here  
```c++  
void ClientConnection::DoReadHeader()  
{  
    auto self(shared_from_this());  
    p_.emplace();  
    p_->body_limit(boost::none);  
    if (timeout_ > 0) {  
        stream_.expires_after(std::chrono::seconds(timeout_));  
    } else {  
        stream_.expires_at(boost::asio::steady_timer::time_point::max());  
    }  
    boost::beast::http::async_read_header(stream_, buffer_, *p_,  
        boost::beast::bind_front_handler(&ClientConnection::OnReadHeader, self));  
}  
  
void ClientConnection::OnReadHeader(boost::beast::error_code ec, std::size_t bytes)  
{  
    boost::ignore_unused(bytes);  
    auto self(shared_from_this());  
    if (ec) {  
        if (ec != boost::beast::http::error::end_of_stream) {  
            LOG_E("[ClientConnection] OnReadHeader error : %s", ec.what().c_str());  
        }  
        auto fun = clientHandler_.GetFun(ClientHandlerType::HEADER_RES_ERROR);  
        if (fun != nullptr) {  
            fun(self);  
        }  
        return DoClose();  
    }  
    auto fun = clientHandler_.GetFun(ClientHandlerType::HEADER_RES);  
    if (fun != nullptr) {  
        fun(self);  
    }  
    if (p_->chunked()) {  
        p_->on_chunk_header(headerCb);  
        p_->on_chunk_body(bodyCb);  
        if (!p_->is_done()) {  
            if (timeout_ > 0) {  
                stream_.expires_after(std::chrono::seconds(timeout_));  
            } else {  
                stream_.expires_at(boost::asio::steady_timer::time_point::max());  
            }  
            boost::beast::http::async_read(stream_, buffer_, *p_,  
                boost::beast::bind_front_handler(&ClientConnection::OnChunkBody, self));  
        }  
    } else {  
        if (!p_->is_done()) {  
            if (timeout_ > 0) {  
                stream_.expires_after(std::chrono::seconds(timeout_));  
            } else {  
                stream_.expires_at(boost::asio::steady_timer::time_point::max());  
            }  
            boost::beast::http::async_read(stream_, buffer_, *p_,  
                boost::beast::bind_front_handler(&ClientConnection::OnReadBody, self));  
        }  
    }  
}  
```   
[ClientConnection.zip](https://github.com/user-attachments/files/17101255/ClientConnection.zip)

---

## Comment 1

> Username: ashtum  
> Created at: 2024-09-23 18:05:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2933#issuecomment-2369005538  

The provided code is incomplete and cannot be compiled or tested. Could you provide more details on the issue?

---

## Comment 2

> Username: xiaoma565  
> Created at: 2024-09-24 06:07:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2933#issuecomment-2370271752  

[client.zip](https://github.com/user-attachments/files/17108988/client.zip)

---

## Comment 3

> Username: ashtum  
> Created at: 2024-09-24 07:30:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2933#issuecomment-2370417420  

It still has some unavailable dependencies. Here is an example that demonstrates how to asynchronously read the header and body in separate steps:  
  
```C++  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/strand.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <memory>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Performs an HTTP GET and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
    tcp::resolver resolver_;  
    beast::tcp_stream stream_;  
    beast::flat_buffer buffer_; // (Must persist between reads)  
    http::request<http::empty_body> req_;  
    http::response<http::string_body> res_;  
    http::response_parser<http::string_body> respr_;  
  
public:  
    // Objects are constructed with a strand to  
    // ensure that handlers do not execute concurrently.  
    explicit  
    session(net::io_context& ioc)  
        : resolver_(net::make_strand(ioc))  
        , stream_(net::make_strand(ioc))  
    {  
    }  
  
    // Start the asynchronous operation  
    void  
    run(  
        char const* host,  
        char const* port,  
        char const* target,  
        int version)  
    {  
        // Set up an HTTP GET request message  
        req_.version(version);  
        req_.method(http::verb::get);  
        req_.target(target);  
        req_.set(http::field::host, host);  
        req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        // Look up the domain name  
        resolver_.async_resolve(  
            host,  
            port,  
            beast::bind_front_handler(  
                &session::on_resolve,  
                shared_from_this()));  
    }  
  
    void  
    on_resolve(  
        beast::error_code ec,  
        tcp::resolver::results_type results)  
    {  
        if(ec)  
            return fail(ec, "resolve");  
  
        // Set a timeout on the operation  
        stream_.expires_after(std::chrono::seconds(30));  
  
        // Make the connection on the IP address we get from a lookup  
        stream_.async_connect(  
            results,  
            beast::bind_front_handler(  
                &session::on_connect,  
                shared_from_this()));  
    }  
  
    void  
    on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type)  
    {  
        if(ec)  
            return fail(ec, "connect");  
  
        // Set a timeout on the operation  
        stream_.expires_after(std::chrono::seconds(30));  
  
        // Send the HTTP request to the remote host  
        http::async_write(stream_, req_,  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
    }  
  
    void  
    on_write(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
          
        // Receive the HTTP response header  
        http::async_read_header(stream_, buffer_, respr_,  
            beast::bind_front_handler(  
                &session::on_header,  
                shared_from_this()));  
    }  
  
    void  
    on_header(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "read");  
  
        std::cout << "headers:" << std::endl;  
        std::cout << respr_.get() << std::endl;  
  
        // Receive the HTTP response body  
        http::async_read(stream_, buffer_, respr_,  
            beast::bind_front_handler(  
                &session::on_body,  
                shared_from_this()));  
    }  
  
    void  
    on_body(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "read");  
  
        std::cout << "body:" << std::endl;  
        std::cout << respr_.get().body() << std::endl;  
  
        // Gracefully close the socket  
        stream_.socket().shutdown(tcp::socket::shutdown_both, ec);  
  
        // not_connected happens sometimes so don't bother reporting it.  
        if(ec && ec != beast::errc::not_connected)  
            return fail(ec, "shutdown");  
  
        // If we get here then the connection is closed gracefully  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char** argv)  
{  
    // Check command line arguments.  
    if(argc != 4 && argc != 5)  
    {  
        std::cerr <<  
            "Usage: http-client-async <host> <port> <target> [<HTTP version: 1.0 or 1.1(default)>]\n" <<  
            "Example:\n" <<  
            "    http-client-async www.example.com 80 /\n" <<  
            "    http-client-async www.example.com 80 / 1.0\n";  
        return EXIT_FAILURE;  
    }  
    auto const host = argv[1];  
    auto const port = argv[2];  
    auto const target = argv[3];  
    int version = argc == 5 && !std::strcmp("1.0", argv[4]) ? 10 : 11;  
  
    // The io_context is required for all I/O  
    net::io_context ioc;  
  
    // Launch the asynchronous operation  
    std::make_shared<session>(ioc)->run(host, port, target, version);  
  
    // Run the I/O service. The call will return when  
    // the get operation is complete.  
    ioc.run();  
  
    return EXIT_SUCCESS;  
}  
```
