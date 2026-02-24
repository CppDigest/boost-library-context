# #1678 - websocket server async-ssl  certificate.pfx  and password [Closed]

> Username: svarogaden  
> Created at: 2019-08-12 19:07:59 UTC  
> Updated at: 2019-09-20 13:13:31 UTC  
> Closed at: 2019-09-20 13:13:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1678  

```  
#include "example/common/server_certificate.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace ssl = boost::asio::ssl;               // from <boost/asio/ssl.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(boost::system::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class session : public std::enable_shared_from_this<session>  
{  
    tcp::socket socket_;  
    websocket::stream<ssl::stream<tcp::socket&>> ws_;  
    boost::asio::strand<  
        boost::asio::io_context::executor_type> strand_;  
    boost::beast::multi_buffer buffer_;  
  
public:  
    // Take ownership of the socket  
    session(tcp::socket socket, ssl::context& ctx)  
        : socket_(std::move(socket))  
        , ws_(socket_, ctx)  
        , strand_(ws_.get_executor())  
    {  
    }  
  
    // Start the asynchronous operation  
    void  
    run()  
    {  
        // Perform the SSL handshake  
        ws_.next_layer().async_handshake(  
            ssl::stream_base::server,  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &session::on_handshake,  
                    shared_from_this(),  
                    std::placeholders::_1)));  
    }  
  
    void  
    on_handshake(boost::system::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
  
        // Accept the websocket handshake  
        ws_.async_accept(  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &session::on_accept,  
                    shared_from_this(),  
                    std::placeholders::_1)));  
    }  
  
    void  
    on_accept(boost::system::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "accept");  
  
        // Read a message  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        // Read a message into our buffer  
        ws_.async_read(  
            buffer_,  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &session::on_read,  
                    shared_from_this(),  
                    std::placeholders::_1,  
                    std::placeholders::_2)));  
    }  
  
    void  
    on_read(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the session was closed  
        if(ec == websocket::error::closed)  
            return;  
  
        if(ec)  
            fail(ec, "read");  
  
        // Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            buffer_.data(),  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &session::on_write,  
                    shared_from_this(),  
                    std::placeholders::_1,  
                    std::placeholders::_2)));  
    }  
  
    void  
    on_write(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        // Clear the buffer  
        buffer_.consume(buffer_.size());  
  
        // Do another read  
        do_read();  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
    ssl::context& ctx_;  
    tcp::acceptor acceptor_;  
    tcp::socket socket_;  
  
public:  
    listener(  
        boost::asio::io_context& ioc,  
        ssl::context& ctx,  
        tcp::endpoint endpoint)  
        : ctx_(ctx)  
        , acceptor_(ioc)  
        , socket_(ioc)  
    {  
        boost::system::error_code ec;  
  
        // Open the acceptor  
        acceptor_.open(endpoint.protocol(), ec);  
        if(ec)  
        {  
            fail(ec, "open");  
            return;  
        }  
  
        // Bind to the server address  
        acceptor_.bind(endpoint, ec);  
        if(ec)  
        {  
            fail(ec, "bind");  
            return;  
        }  
  
        // Start listening for connections  
        acceptor_.listen(  
            boost::asio::socket_base::max_listen_connections, ec);  
        if(ec)  
        {  
            fail(ec, "listen");  
            return;  
        }  
    }  
  
    // Start accepting incoming connections  
    void  
    run()  
    {  
        if(! acceptor_.is_open())  
            return;  
        do_accept();  
    }  
  
    void  
    do_accept()  
    {  
        acceptor_.async_accept(  
            socket_,  
            std::bind(  
                &listener::on_accept,  
                shared_from_this(),  
                std::placeholders::_1));  
    }  
  
    void  
    on_accept(boost::system::error_code ec)  
    {  
        if(ec)  
        {  
            fail(ec, "accept");  
        }  
        else  
        {  
            // Create the session and run it  
            std::make_shared<session>(std::move(socket_), ctx_)->run();  
        }  
  
        // Accept another connection  
        do_accept();  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[])  
{  
    // Check command line arguments.  
    if (argc != 4)  
    {  
        std::cerr <<  
            "Usage: websocket-server-async-ssl <address> <port> <threads>\n" <<  
            "Example:\n" <<  
            "    websocket-server-async-ssl 0.0.0.0 8080 1\n";  
        return EXIT_FAILURE;  
    }  
    auto const address = boost::asio::ip::make_address(argv[1]);  
    auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto const threads = std::max<int>(1, std::atoi(argv[3]));  
  
    // The io_context is required for all I/O  
    boost::asio::io_context ioc{threads};  
      
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::sslv23};  
  
    // This holds the self-signed certificate used by the server  
    load_server_certificate(ctx);  
  
    // Create and launch a listening port  
    std::make_shared<listener>(ioc, ctx, tcp::endpoint{address, port})->run();  
  
    // Run the I/O service on the requested number of threads  
    std::vector<std::thread> v;  
    v.reserve(threads - 1);  
    for(auto i = threads - 1; i > 0; --i)  
        v.emplace_back(  
        [&ioc]  
        {  
            ioc.run();  
        });  
    ioc.run();  
  
    return EXIT_SUCCESS;  
}  
```  
	  
I took an example web socket server from here:  
https://www.boost.org/doc/libs/1_66_0/libs/beast/example/websocket/server/async-ssl/websocket_server_async_ssl.cpp  
I have a domain to which I received an ssl certificate.  
I have a certificate in this format - certificate.pfx , and password.     
How to apply my certificate in this code?  
In which function is it possible to download an ssl certificate with a password.  
I guess this is at this point in the code?  
  
  
// The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::sslv23};  
  
    // This holds the self-signed certificate used by the server  
    load_server_certificate(ctx);

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-12 19:43:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1678#issuecomment-520567286  

I have no idea. The SSL part comes from Asio not Beast, and I am by no means an expert on dealing with certificates.

---

## Comment 2

> Username: leleftheriades  
> Created at: 2019-08-14 11:51:49 UTC  
> Updated at: 2019-08-14 12:01:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1678#issuecomment-521214526  

Maybe this example will help, we use pem certificates, but it may inspire you  
  
std::string password_callback(std::size_t max_length, boost::asio::ssl::context::password_purpose purpose){  
    return "test";  
}  
std::string certificate = "-----BEGIN CERTIFICATE-----\n....";  
std::string key="-----BEGIN PRIVATE KEY-----\n...";  
  
boost::asio::ssl::context ctx(boost::asio::ssl::context::tlsv12);  
ctx.set_password_callback(password_callback); //<-- is it this one?  
ctx.use_certificate(boost::asio::const_buffer(certificate.c_str(), certificate.size()), boost::asio::ssl::context::pem);  
ctx.use_private_key(boost::asio::const_buffer(key.c_str(), key.size()), boost::asio::ssl::context::file_format::pem);  
 ctx.set_verify_mode(boost::asio::ssl::verify_none);

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-09-13 12:36:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1678#issuecomment-531220119  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-09-20 13:13:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1678#issuecomment-533548830  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
