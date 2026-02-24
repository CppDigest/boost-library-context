# #2912 - No able to get the full path while running Async SSL websock code. [Closed]

> Username: shivank1111  
> Created at: 2024-07-30 12:57:59 UTC  
> Updated at: 2024-10-03 12:12:36 UTC  
> Closed at: 2024-10-03 12:12:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2912  

Version - 1.83  
  
I am not able to get the endpoint(Full path of the connection- like wss://server-name.com/username1/..). I am trying the get the full endpoint in on_accept class of listener.cpp and during websocket handshake.(In on_run() function) but still not successful in getting.  
  
I am only getting the IP and port from which the connection came.  
  
Kindly help me with the same.  
I am using the basic example of websocket_server_async_ssl.cpp and also attaching the code.  
  
```//  
// Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: WebSocket SSL server, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include "example/common/server_certificate.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/asio/dispatch.hpp>  
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
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
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
    websocket::stream<  
        beast::ssl_stream<beast::tcp_stream>> ws_;  
    beast::flat_buffer buffer_;  
public:  
    // Take ownership of the socket  
    session(tcp::socket&& socket, ssl::context& ctx)  
        : ws_(std::move(socket), ctx)  
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
        // Set the timeout.  
        beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
  
         // Perform the SSL handshake  
        ws_.next_layer().async_handshake(  
            ssl::stream_base::server,  
            beast::bind_front_handler(  
                &session::on_handshake,  
                shared_from_this()));  
    }  
  
    void  
    on_handshake(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
  
        // Turn off the timeout on the tcp_stream, because  
        // the websocket stream has its own timeout system.  
        beast::get_lowest_layer(ws_).expires_never();  
  
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
                        " websocket-server-async-ssl");  
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
  
        // Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            buffer_.data(),  
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
    net::io_context& ioc_;  
    ssl::context& ctx_;  
    tcp::acceptor acceptor_;  
  
public:  
    listener(  
        net::io_context& ioc,  
        ssl::context& ctx,  
        tcp::endpoint endpoint)  
        : ioc_(ioc)  
        , ctx_(ctx)  
        , acceptor_(net::make_strand(ioc))  
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
            std::make_shared<session>(std::move(socket), ctx_)->run();  
        }  
  
        // Accept another connection  
        do_accept();  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[])  
{  
    std::cout<<"main-1"<<"\n";  
	// Check command line arguments.  
    if (argc != 4)  
    {  
        std::cerr <<  
            "Usage: websocket-server-async-ssl <address> <port> <threads>\n" <<  
            "Example:\n" <<  
            "    websocket-server-async-ssl 0.0.0.0 8080 1\n";  
        return EXIT_FAILURE;  
    }  
    auto const address = net::ip::make_address(argv[1]);  
    auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto const threads = std::max<int>(1, std::atoi(argv[3]));  
    // The io_context is required for all I/O  
    net::io_context ioc{threads};  
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::tlsv12};  
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

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-07-30 13:03:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2258302016  

You need to do a "reverse DNS lookup" on the IP if you want to get the name.

---

## Comment 2

> Username: shivank1111  
> Created at: 2024-07-30 14:14:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2258463513  

Hi @vinniefalco , I tried using reverse DNS lookup but it is giving IP and port only. I need to get the full endpoint because I am doing my other tasks such as no same url path channel allowed etc, for that I need to get the complete endpoint.  
  
Example:"wss://server-name.com/username1/..". The major part is from "/username1/..".

---

## Comment 3

> Username: ashtum  
> Created at: 2024-07-30 14:27:00 UTC  
> Updated at: 2024-07-30 14:34:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2258491655  

First, you need to read the initial HTTP upgrade request (just like a regular http request) and inspect the request target. Then, use this overload of `async_accept` to continue the accept operation: [websocket::stream::async_accept (3 of 3 overloads)](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html).  
Here is an example of it: [Inspecting HTTP Requests](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests)

---

## Comment 4

> Username: shivank1111  
> Created at: 2024-07-31 05:32:46 UTC  
> Updated at: 2024-07-31 05:56:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2259705017  

Hi @ashtum, I am taking the chat-multi code example(/example/websocket/server/chat-multi) and in that I have replaced the listener.cpp, websocket_session.cpp and http_session.cpp with websocket_server_async_ssl.cpp and http_server_async_ssl.cpp because I want to test with SSL but the issue is that the https part is working fine but if we sending the "wss" connection then it is going to websocket_session::on_run() function and not able to do handshake(in websocket_session::on_handshake) function.   
  
My observations is that during the opening of connection it is going to http_session::on-run and from there it is going to listener::do_accept and then from there to http_session::on_handshake function and then if it is a websocket upgrade then it proceed accordingly.   
I am able to get the endpoint using websocket constructor, but the connection is showing connecting on the Postman side and in my code it is stuck at websocket_session::on_run and after trying to few seconds(30 seconds- in my case) it just goes to websocket_session::on_handshake function and before disconnecting it just foes and comes out of the handshake function.   
  
It should go to listener::do_accept and then to websocket_session::on_handshake and then to websocket_session::on_accept, but it is not going.  
  
Can you please help me with the same.  
  
I even tried by doing ssl handshake only in https and not in websocket part but it is not even accepting the wss connection.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-07-31 06:59:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2259810918  

It's difficult to determine the issue without seeing the code. Please check if the client successfully completes the SSL handshake. If you receive the HTTP upgrade request separately, use this overload of [websocket::stream::async_accept](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html) and provide the initial upgrade request.

---

## Comment 6

> Username: shivank1111  
> Created at: 2024-08-05 12:18:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2268937112  

Hi @ashtum, I have made a sample code but here it is not possible to send all the files(.hpp and .cpp), so can you please share you email ID where I will send you the code.

---

## Comment 7

> Username: ashtum  
> Created at: 2024-08-05 12:44:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2268987959  

> Hi @ashtum, I have made a sample code but here it is not possible to send all the files(.hpp and .cpp), so can you please share you email ID where I will send you the code.  
  
If the issue is the number of files, you can create a gist. If the issue is privacy, you can send me at ashtumashtum@gmail.com.

---

## Comment 8

> Username: shivank1111  
> Created at: 2024-08-06 07:30:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2270578126  

Thankyou @ashtum, will share you through email.

---

## Comment 9

> Username: ashtum  
> Created at: 2024-08-06 11:13:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2271033271  

You discard the request in this line:  
```C++  
template<class Body, class Allocator> void websocket_session::run(http::request<Body, http::basic_fields<Allocator>> req, int timeout_value)  
{  
	std::cout<<"websocket session run"<<"\n";  
	net::dispatch(ws_.get_executor(), beast::bind_front_handler( &websocket_session::on_run, shared_from_this()));  
}  
```  
You need to use this overload of [websocket::stream::async_accept](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html) and provide the initial upgrade request.

---

## Comment 10

> Username: shivank1111  
> Created at: 2024-08-06 12:33:01 UTC  
> Updated at: 2024-08-06 12:34:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2271180799  

Hi @ashtum, I tried changing by discarding the below code  
``template<class Body, class Allocator> void websocket_session::run(http::request<Body, http::basic_fields<Allocator>> req, int timeout_value)  
{  
	std::cout<<"websocket session run"<<"\n";  
	net::dispatch(ws_.get_executor(), beast::bind_front_handler( &websocket_session::on_run, shared_from_this()));  
}``  
  
and overload by using async_accept but I am not able to do it correctly.   
  
Can you please help me with this part, I am not able to understand properly.

---

## Comment 11

> Username: shivank1111  
> Created at: 2024-08-06 12:54:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2271222805  

Hii all,   
This is the link to all the files.  
  
https://gist.github.com/shivank1111/3b5864811a8a1e819cc7c161b63aa741

---

## Comment 12

> Username: ashtum  
> Created at: 2024-08-06 12:54:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2271223101  

You need to pass the `req` to [websocket::stream::async_accept](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html). see how it is done in this example: https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L509  
  
There is more issue in your code, like here you try to do another ssl handshake:  
```C++  
void websocket_session::on_run()  
{  
	std::cout<<"Websocket session on_run"<<"\n";  
	  
	beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
	 // Perform the SSL handshake  
	ws_.next_layer().async_handshake( ssl::stream_base::server, beast::bind_front_handler( &websocket_session::on_handshake, shared_from_this()));  
  
```  
I would recommend begin with [advanced_server_flex](https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L509) and modify it to your need.

---

## Comment 13

> Username: shivank1111  
> Created at: 2024-08-06 12:56:39 UTC  
> Updated at: 2024-08-06 12:57:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2912#issuecomment-2271228011  

Okay @ashtum, thanks for the help. I will begin from advanced_server_flex and the proceed accordingly.
