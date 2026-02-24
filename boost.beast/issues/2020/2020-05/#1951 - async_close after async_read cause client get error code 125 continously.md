# #1951 - async_close after async_read cause client get error code 125 continously [Closed]

> Username: baker-Xie  
> Created at: 2020-05-12 13:18:08 UTC  
> Updated at: 2020-05-12 13:54:32 UTC  
> Closed at: 2020-05-12 13:54:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1951  

Boost Version: 1.73  
Beast Version : 290  
  
Dear all, I meet a problem:  
(1) The ws_async_client connect to ws_async_server  
(2) after connection, both server and client read message using async_read  
(3) everything is normal  
(4) Then user close the client side by async_close (simulated by using a thread)  
(5) The server side will read websocket::error::closed in async_read's handler and return  
(6) The client side read error code 125, not websocket::error::closed, if I read again, error 125 will occur again and again  
  
I want to know, if I async_close in the client side,the server will close gracefully, **but how the client close gracefully?**   
  
The test code is from beast example, and did some modification:  
server.cpp  
  
```  
//------------------------------------------------------------------------------  
//  
// Example: WebSocket server, asynchronous  
//  
//------------------------------------------------------------------------------  
#include <boost/beast/version.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/asio/strand.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class session : public std::enable_shared_from_this<session>  
{  
    websocket::stream<beast::tcp_stream> ws_;  
    beast::flat_buffer buffer_;  
  
public:  
    // Take ownership of the socket  
    explicit  
    session(tcp::socket&& socket)  
            : ws_(std::move(socket))  
    {  
    }  
  
    // Get on the correct executor  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(ws_.get_executor(),  
                      beast::bind_front_handler(  
                              &session::on_run,  
                              shared_from_this()));  
    }  
  
    // Start the asynchronous operation  
    void  
    on_run()  
    {  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(  
                websocket::stream_base::timeout::suggested(  
                        beast::role_type::server));  
  
        // Set a decorator to change the Server of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
                [](websocket::response_type& res)  
                {  
                    res.set(http::field::server,  
                            std::string(BOOST_BEAST_VERSION_STRING) +  
                            " websocket-server-async");  
                }));  
        // Accept the websocket handshake  
        ws_.async_accept(  
                beast::bind_front_handler(  
                        &session::on_accept,  
                        shared_from_this()));  
    }  
  
    void  
    on_accept(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "accept");  
        printf("New Connection\n");  
        // Read a message  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        // Read a message into our buffer  
        ws_.async_read(  
                buffer_,  
                beast::bind_front_handler(  
                        &session::on_read,  
                        shared_from_this()));  
    }  
  
    void  
    on_read(  
            beast::error_code ec,  
            std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the session was closed  
        if(ec == websocket::error::closed)  
            return;  
  
        if(ec)  
            fail(ec, "read");  
  
        do_read();  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
    net::io_context& ioc_;  
    tcp::acceptor acceptor_;  
  
public:  
    listener(  
            net::io_context& ioc,  
            tcp::endpoint endpoint)  
            : ioc_(ioc)  
            , acceptor_(ioc)  
    {  
        beast::error_code ec;  
  
        // Open the acceptor  
        acceptor_.open(endpoint.protocol(), ec);  
        if(ec)  
        {  
            fail(ec, "open");  
            return;  
        }  
  
        // Allow address reuse  
        acceptor_.set_option(net::socket_base::reuse_address(true), ec);  
        if(ec)  
        {  
            fail(ec, "set_option");  
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
                net::socket_base::max_listen_connections, ec);  
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
        do_accept();  
    }  
  
private:  
    void  
    do_accept()  
    {  
        // The new connection gets its own strand  
        acceptor_.async_accept(  
                net::make_strand(ioc_),  
                beast::bind_front_handler(  
                        &listener::on_accept,  
                        shared_from_this()));  
    }  
  
    void  
    on_accept(beast::error_code ec, tcp::socket socket)  
    {  
        if(ec)  
        {  
            fail(ec, "accept");  
        }  
        else  
        {  
            // Create the session and run it  
            std::make_shared<session>(std::move(socket))->run();  
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
                  "Usage: websocket-server-async <address> <port> <threads>\n" <<  
                  "Example:\n" <<  
                  "    websocket-server-async 0.0.0.0 8080 1\n";  
        return EXIT_FAILURE;  
    }  
    auto const address = net::ip::make_address(argv[1]);  
    auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto const threads = std::max<int>(1, std::atoi(argv[3]));  
  
    // The io_context is required for all I/O  
    net::io_context ioc{threads};  
  
    // Create and launch a listening port  
    std::make_shared<listener>(ioc, tcp::endpoint{address, port})->run();  
  
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
  
client.cpp  
  
  
```  
//------------------------------------------------------------------------------  
//  
// Example: WebSocket client, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/strand.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Sends a WebSocket message and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
    tcp::resolver resolver_;  
    websocket::stream<beast::tcp_stream> ws_;  
    beast::flat_buffer buffer_;  
    std::string host_;  
    std::string text_;  
  
public:  
    // Resolver and socket require an io_context  
    explicit  
    session(net::io_context& ioc)  
            : resolver_(net::make_strand(ioc))  
            , ws_(net::make_strand(ioc))  
    {  
    }  
  
    // Start the asynchronous operation  
    void  
    run(  
            char const* host,  
            char const* port,  
            char const* text)  
    {  
        // Save these for later  
        host_ = host;  
        text_ = text;  
  
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
  
        // Set the timeout for the operation  
        beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(ws_).async_connect(  
                results,  
                beast::bind_front_handler(  
                        &session::on_connect,  
                        shared_from_this()));  
    }  
  
    void  
    on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type ep)  
    {  
        if(ec)  
            return fail(ec, "connect");  
  
        // Turn off the timeout on the tcp_stream, because  
        // the websocket stream has its own timeout system.  
        beast::get_lowest_layer(ws_).expires_never();  
  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(  
                websocket::stream_base::timeout::suggested(  
                        beast::role_type::client));  
  
        // Set a decorator to change the User-Agent of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
                [](websocket::request_type& req)  
                {  
                    req.set(http::field::user_agent,  
                            std::string(BOOST_BEAST_VERSION_STRING) +  
                            " websocket-client-async");  
                }));  
  
        // Update the host_ string. This will provide the value of the  
        // Host HTTP header during the WebSocket handshake.  
        // See https://tools.ietf.org/html/rfc7230#section-5.4  
        host_ += ':' + std::to_string(ep.port());  
  
        // Perform the websocket handshake  
        ws_.async_handshake(host_, "/",  
                            beast::bind_front_handler(  
                                    &session::on_handshake,  
                                    shared_from_this()));  
    }  
  
    void  
    on_handshake(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
  
        do_read();  
    }  
  
    void do_read()  
    {  
        ws_.async_read(  
                buffer_,  
                beast::bind_front_handler(  
                        &session::on_read,  
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
  
        // Read a message into our buffer  
  
    }  
  
    void  
    on_read(  
            beast::error_code ec,  
            std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
        if (ec)  
        {  
            if (ec == websocket::error::closed)  
                std::cout << "closed nicely\n";  
            else  
                std::cout << ec.message() << std::endl;  
        }  
        else  
        {  
            do_read();  
        }  
    }  
  
    void do_close()  
    {  
        net::post(ws_.get_executor(),[self = this->shared_from_this()]  
        {  
            self->ws_.async_close(websocket::close_code::normal,beast::bind_front_handler(  
                    &session::on_close,  
                    self)) ;  
        });  
    }  
  
    void  
    on_close(beast::error_code ec)  
    {  
        if(ec)  
            fail(ec, "close");  
  
        // If we get here then the connection is closed gracefully  
  
        // The make_printable() function helps print a ConstBufferSequence  
        std::cout << beast::make_printable(buffer_.data()) << std::endl;  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char** argv)  
{  
    // Check command line arguments.  
    if(argc != 4)  
    {  
        std::cerr <<  
                  "Usage: websocket-client-async <host> <port> <text>\n" <<  
                  "Example:\n" <<  
                  "    websocket-client-async echo.websocket.org 80 \"Hello, world!\"\n";  
        return EXIT_FAILURE;  
    }  
    auto const host = argv[1];  
    auto const port = argv[2];  
    auto const text = argv[3];  
  
    // The io_context is required for all I/O  
    net::io_context ioc;  
    // Launch the asynchronous operation  
    auto sp = std::make_shared<session>(ioc);  
    sp->run(host, port, text);  
  
    std::thread t([&]  
                  {  
                      ioc.run();  
                  });  
    std::thread t2([&]  
                  {  
                      usleep(3e6);  
                      sp->do_close();  
                  });  
    t.join();  
    t2.join();  
    return EXIT_SUCCESS;  
}  
```

---

## Comment 1

> Username: baker-Xie  
> Created at: 2020-05-12 13:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1951#issuecomment-627360852  

Problem solved. Change the client code to the following.  
```  
void  
    on_read(  
            beast::error_code ec,  
            std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
        if (ec)  
        {  
            if (ec == websocket::error::closed || ec.value() == 125)  
                std::cout << "closed nicely\n";  
            else  
                std::cout << ec.message() << std::endl;  
        }  
        else  
        {  
            do_read();  
        }  
    }  
```
