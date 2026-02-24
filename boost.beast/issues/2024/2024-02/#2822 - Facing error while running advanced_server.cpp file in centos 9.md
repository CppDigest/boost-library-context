# #2822 - Facing error while running advanced_server.cpp file in centos 9. [Closed]

> Username: shivank1111  
> Created at: 2024-02-15 10:08:49 UTC  
> Updated at: 2024-02-20 10:25:12 UTC  
> Closed at: 2024-02-20 10:25:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2822  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
boost_1_82_0 version  
  
### Steps necessary to reproduce the problem  
  
Only run on any centos 9 machine  
  
### All relevant compiler information  
  
I tried with "g++  advanced_server.cpp -lpthread -lboost_system -lboost_filesystem -lboost_coroutine -o websock" and   
"g++  advanced_server.cpp -lpthread -lboost_system -lboost_filesystem -lboost_coroutine -I/home/beast-develop/include/boost -o websock" but error is persistent.   
  
I have also replaced the file of "message_generator.hpp" in all the available places.   
![Error](https://github.com/boostorg/beast/assets/160120467/6c47b350-da04-4b65-b1cc-779f691d6abe)  
  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: ashtum  
> Created at: 2024-02-15 10:32:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1945791878  

Could you please provide your sample code?  
  
> I have also replaced the file of "message_generator.hpp" in all the available places.  
  
`advanced_server.cpp` is already using `message_generator` what did you replace with?

---

## Comment 2

> Username: shivank1111  
> Created at: 2024-02-15 10:48:10 UTC  
> Updated at: 2024-02-15 11:13:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1945831411  

https://www.boost.org/doc/libs/1_82_0/boost/beast/http/message_generator.hpp   
  
This code only, the same code only.  
Sample code of advanced_server.cpp is from here only "https://github.com/boostorg/beast/tree/develop/example/advanced/server"  
  
I don't know but while compiling only, it's giving these errors. I have tried several times but still the issue is persisting. Kindly help me in solving this issue.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-02-15 11:27:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1945905367  

Could you please provide the exact code you are trying to compile?

---

## Comment 4

> Username: shivank1111  
> Created at: 2024-02-15 11:33:11 UTC  
> Updated at: 2024-02-15 12:20:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1945915033  

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
// Example: Advanced server  
//  
//------------------------------------------------------------------------------  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/asio/signal_set.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/make_unique.hpp>  
#include <boost/optional.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <queue>  
#include <string>  
#include <thread>  
#include <vector>  
  
namespace beast = boost::beast;                 // from <boost/beast.hpp>  
namespace http = beast::http;                   // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket;         // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;                    // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
  
// Return a reasonable mime type based on the extension of a file.  
beast::string_view  
mime_type(beast::string_view path)  
{  
    using beast::iequals;  
    auto const ext = [&path]  
    {  
        auto const pos = path.rfind(".");  
        if(pos == beast::string_view::npos)  
            return beast::string_view{};  
        return path.substr(pos);  
    }();  
    if(iequals(ext, ".htm"))  return "text/html";  
    if(iequals(ext, ".html")) return "text/html";  
    if(iequals(ext, ".php"))  return "text/html";  
    if(iequals(ext, ".css"))  return "text/css";  
    if(iequals(ext, ".txt"))  return "text/plain";  
    if(iequals(ext, ".js"))   return "application/javascript";  
    if(iequals(ext, ".json")) return "application/json";  
    if(iequals(ext, ".xml"))  return "application/xml";  
    if(iequals(ext, ".swf"))  return "application/x-shockwave-flash";  
    if(iequals(ext, ".flv"))  return "video/x-flv";  
    if(iequals(ext, ".png"))  return "image/png";  
    if(iequals(ext, ".jpe"))  return "image/jpeg";  
    if(iequals(ext, ".jpeg")) return "image/jpeg";  
    if(iequals(ext, ".jpg"))  return "image/jpeg";  
    if(iequals(ext, ".gif"))  return "image/gif";  
    if(iequals(ext, ".bmp"))  return "image/bmp";  
    if(iequals(ext, ".ico"))  return "image/vnd.microsoft.icon";  
    if(iequals(ext, ".tiff")) return "image/tiff";  
    if(iequals(ext, ".tif"))  return "image/tiff";  
    if(iequals(ext, ".svg"))  return "image/svg+xml";  
    if(iequals(ext, ".svgz")) return "image/svg+xml";  
    return "application/text";  
}  
  
// Append an HTTP rel-path to a local filesystem path.  
// The returned path is normalized for the platform.  
std::string  
path_cat(  
    beast::string_view base,  
    beast::string_view path)  
{  
    if(base.empty())  
        return std::string(path);  
    std::string result(base);  
#ifdef BOOST_MSVC  
    char constexpr path_separator = '\\';  
    if(result.back() == path_separator)  
        result.resize(result.size() - 1);  
    result.append(path.data(), path.size());  
    for(auto& c : result)  
        if(c == '/')  
            c = path_separator;  
#else  
    char constexpr path_separator = '/';  
    if(result.back() == path_separator)  
        result.resize(result.size() - 1);  
    result.append(path.data(), path.size());  
#endif  
    return result;  
}  
  
// Return a response for the given request.  
//  
// The concrete type of the response message (which depends on the  
// request), is type-erased in message_generator.  
template <class Body, class Allocator>  
http::message_generator  
handle_request(  
    beast::string_view doc_root,  
    http::request<Body, http::basic_fields<Allocator>>&& req)  
{  
    // Returns a bad request response  
    auto const bad_request =  
    [&req](beast::string_view why)  
    {  
        http::response<http::string_body> res{http::status::bad_request, req.version()};  
        res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
        res.set(http::field::content_type, "text/html");  
        res.keep_alive(req.keep_alive());  
        res.body() = std::string(why);  
        res.prepare_payload();  
        return res;  
    };  
  
    // Returns a not found response  
    auto const not_found =  
    [&req](beast::string_view target)  
    {  
        http::response<http::string_body> res{http::status::not_found, req.version()};  
        res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
        res.set(http::field::content_type, "text/html");  
        res.keep_alive(req.keep_alive());  
        res.body() = "The resource '" + std::string(target) + "' was not found.";  
        res.prepare_payload();  
        return res;  
    };  
  
    // Returns a server error response  
    auto const server_error =  
    [&req](beast::string_view what)  
    {  
        http::response<http::string_body> res{http::status::internal_server_error, req.version()};  
        res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
        res.set(http::field::content_type, "text/html");  
        res.keep_alive(req.keep_alive());  
        res.body() = "An error occurred: '" + std::string(what) + "'";  
        res.prepare_payload();  
        return res;  
    };  
  
    // Make sure we can handle the method  
    if( req.method() != http::verb::get &&  
        req.method() != http::verb::head)  
        return bad_request("Unknown HTTP-method");  
  
    // Request path must be absolute and not contain "..".  
    if( req.target().empty() ||  
        req.target()[0] != '/' ||  
        req.target().find("..") != beast::string_view::npos)  
        return bad_request("Illegal request-target");  
  
    // Build the path to the requested file  
    std::string path = path_cat(doc_root, req.target());  
    if(req.target().back() == '/')  
        path.append("index.html");  
  
    // Attempt to open the file  
    beast::error_code ec;  
    http::file_body::value_type body;  
    body.open(path.c_str(), beast::file_mode::scan, ec);  
  
    // Handle the case where the file doesn't exist  
    if(ec == beast::errc::no_such_file_or_directory)  
        return not_found(req.target());  
  
    // Handle an unknown error  
    if(ec)  
        return server_error(ec.message());  
  
    // Cache the size since we need it after the move  
    auto const size = body.size();  
  
    // Respond to HEAD request  
    if(req.method() == http::verb::head)  
    {  
        http::response<http::empty_body> res{http::status::ok, req.version()};  
        res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
        res.set(http::field::content_type, mime_type(path));  
        res.content_length(size);  
        res.keep_alive(req.keep_alive());  
        return res;  
    }  
  
    // Respond to GET request  
    http::response<http::file_body> res{  
        std::piecewise_construct,  
        std::make_tuple(std::move(body)),  
        std::make_tuple(http::status::ok, req.version())};  
    res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
    res.set(http::field::content_type, mime_type(path));  
    res.content_length(size);  
    res.keep_alive(req.keep_alive());  
    return res;  
}  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class websocket_session : public std::enable_shared_from_this<websocket_session>  
{  
    websocket::stream<beast::tcp_stream> ws_;  
    beast::flat_buffer buffer_;  
  
public:  
    // Take ownership of the socket  
    explicit  
    websocket_session(tcp::socket&& socket)  
        : ws_(std::move(socket))  
    {  
    }  
  
    // Start the asynchronous accept operation  
    template<class Body, class Allocator>  
    void  
    do_accept(http::request<Body, http::basic_fields<Allocator>> req)  
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
                        " advanced-server");  
            }));  
  
        // Accept the websocket handshake  
        ws_.async_accept(  
            req,  
            beast::bind_front_handler(  
                &websocket_session::on_accept,  
                shared_from_this()));  
    }  
  
private:  
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
                &websocket_session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the websocket_session was closed  
        if(ec == websocket::error::closed)  
            return;  
  
        if(ec)  
            fail(ec, "read");  
	  
        // Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            buffer_.data(),  
            beast::bind_front_handler(  
                &websocket_session::on_write,  
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
  
// Handles an HTTP server connection  
class http_session : public std::enable_shared_from_this<http_session>  
{  
    beast::tcp_stream stream_;  
    beast::flat_buffer buffer_;  
    std::shared_ptr<std::string const> doc_root_;  
  
    static constexpr std::size_t queue_limit = 8; // max responses  
    std::queue<http::message_generator> response_queue_;  
  
    // The parser is stored in an optional container so we can  
    // construct it from scratch it at the beginning of each new message.  
    boost::optional<http::request_parser<http::string_body>> parser_;  
  
public:  
    // Take ownership of the socket  
    http_session(  
        tcp::socket&& socket,  
        std::shared_ptr<std::string const> const& doc_root)  
        : stream_(std::move(socket))  
        , doc_root_(doc_root)  
    {  
        static_assert(queue_limit > 0,  
                      "queue limit must be positive");  
    }  
  
    // Start the session  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(  
            stream_.get_executor(),  
            beast::bind_front_handler(  
                &http_session::do_read,  
                this->shared_from_this()));  
    }  
  
private:  
    void  
    do_read()  
    {  
        // Construct a new parser for each message  
        parser_.emplace();  
  
        // Apply a reasonable limit to the allowed size  
        // of the body in bytes to prevent abuse.  
        parser_->body_limit(10000);  
  
        // Set the timeout.  
        stream_.expires_after(std::chrono::seconds(30));  
  
        // Read a request using the parser-oriented interface  
        http::async_read(  
            stream_,  
            buffer_,  
            *parser_,  
            beast::bind_front_handler(  
                &http_session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_read(beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            return do_close();  
  
        if(ec)  
            return fail(ec, "read");  
  
        // See if it is a WebSocket Upgrade  
        if(websocket::is_upgrade(parser_->get()))  
        {  
            // Create a websocket session, transferring ownership  
            // of both the socket and the HTTP request.  
            std::make_shared<websocket_session>(  
                stream_.release_socket())->do_accept(parser_->release());  
            return;  
        }  
  
        // Send the response  
        queue_write(handle_request(*doc_root_, parser_->release()));  
  
        // If we aren't at the queue limit, try to pipeline another request  
        if (response_queue_.size() < queue_limit)  
            do_read();  
    }  
  
    void  
    queue_write(http::message_generator response)  
    {  
        // Allocate and store the work  
        response_queue_.push(std::move(response));  
  
        // If there was no previous work, start the write loop  
        if (response_queue_.size() == 1)  
            do_write();  
    }  
  
    // Called to start/continue the write-loop. Should not be called when  
    // write_loop is already active.  
    void  
    do_write()  
    {  
        if(! response_queue_.empty())  
        {  
            bool keep_alive = response_queue_.front().keep_alive();  
  
            beast::async_write(  
                stream_,  
                std::move(response_queue_.front()),  
                beast::bind_front_handler(  
                    &http_session::on_write,  
                    shared_from_this(),  
                    keep_alive));  
        }  
    }  
  
    void  
    on_write(  
        bool keep_alive,  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        if(! keep_alive)  
        {  
            // This means we should close the connection, usually because  
            // the response indicated the "Connection: close" semantic.  
            return do_close();  
        }  
  
        // Resume the read if it has been paused  
        if(response_queue_.size() == queue_limit)  
            do_read();  
  
        response_queue_.pop();  
  
        do_write();  
    }  
  
    void  
    do_close()  
    {  
        // Send a TCP shutdown  
        beast::error_code ec;  
        stream_.socket().shutdown(tcp::socket::shutdown_send, ec);  
  
        // At this point the connection is closed gracefully  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
    net::io_context& ioc_;  
    tcp::acceptor acceptor_;  
    std::shared_ptr<std::string const> doc_root_;  
  
public:  
    listener(  
        net::io_context& ioc,  
        tcp::endpoint endpoint,  
        std::shared_ptr<std::string const> const& doc_root)  
        : ioc_(ioc)  
        , acceptor_(net::make_strand(ioc))  
        , doc_root_(doc_root)  
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
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(  
            acceptor_.get_executor(),  
            beast::bind_front_handler(  
                &listener::do_accept,  
                this->shared_from_this()));  
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
            // Create the http session and run it  
            std::make_shared<http_session>(  
                std::move(socket),  
                doc_root_)->run();  
        }  
  
        // Accept another connection  
        do_accept();  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[])  
{  
    // Check command line arguments.  
    if (argc != 5)  
    {  
        std::cerr <<  
            "Usage: advanced-server <address> <port> <doc_root> <threads>\n" <<  
            "Example:\n" <<  
            "    advanced-server 0.0.0.0 8080 . 1\n";  
        return EXIT_FAILURE;  
    }  
    auto const address = net::ip::make_address(argv[1]);  
    auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto const doc_root = std::make_shared<std::string>(argv[3]);  
    auto const threads = std::max<int>(1, std::atoi(argv[4]));  
  
    // The io_context is required for all I/O  
    net::io_context ioc{threads};  
  
    // Create and launch a listening port  
    std::make_shared<listener>(  
        ioc,  
        tcp::endpoint{address, port},  
        doc_root)->run();  
  
    // Capture SIGINT and SIGTERM to perform a clean shutdown  
    net::signal_set signals(ioc, SIGINT, SIGTERM);  
    signals.async_wait(  
        [&](beast::error_code const&, int)  
        {  
            // Stop the `io_context`. This will cause `run()`  
            // to return immediately, eventually destroying the  
            // `io_context` and all of the sockets in it.  
            ioc.stop();  
        });  
  
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
  
    // (If we get here, it means we got a SIGINT or SIGTERM)  
  
    // Block until all the threads exit  
    for(auto& t : v)  
        t.join();  
  
    return EXIT_SUCCESS;  
}  
```

---

## Comment 5

> Username: ashtum  
> Created at: 2024-02-15 11:36:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1945919873  

Please use a [code block](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-and-highlighting-code-blocks).

---

## Comment 6

> Username: shivank1111  
> Created at: 2024-02-15 12:23:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1945993448  

Apologies. Updated!

---

## Comment 7

> Username: ashtum  
> Created at: 2024-02-15 12:32:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1946007643  

`message_generator` has been added to Beast in Boost 1.81. It appears that you are using an older version of Boost.

---

## Comment 8

> Username: ashtum  
> Created at: 2024-02-15 12:37:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1946017836  

If you don't have an up to date version of boost in your OS repository, you can follow the guideline [here](https://www.preview.boost.org/doc/user-guide/getting-started.html) to build a new version from srouce.

---

## Comment 9

> Username: shivank1111  
> Created at: 2024-02-15 12:46:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1946030759  

Hi, I already have the latest versions. Moreover the important version is of Boost and that I am using Boost 1.82 version.

---

## Comment 10

> Username: ashtum  
> Created at: 2024-02-15 12:58:28 UTC  
> Updated at: 2024-02-15 13:03:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1946049879  

This is your code compiling fine using boost.1.82: https://godbolt.org/z/W3Wj1oEhr  
  
Can you check your version of boost?  
  
```C++  
#include <boost/version.hpp>  
  
#include <iostream>  
  
int main()  
{  
    std::cout << BOOST_LIB_VERSION << std::endl;  
}  
```

---

## Comment 11

> Username: shivank1111  
> Created at: 2024-02-16 05:19:56 UTC  
> Updated at: 2024-02-16 05:25:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1947775680  

Hi, sorry for the late reply.  
  
I have tried again after installing boost_1_84_0 version but while the above version program gave the output as "1_75". When I was using boost_1_82_0 version also,  
![version](https://github.com/boostorg/beast/assets/160120467/392a2c02-9689-4363-b90c-9a6818faa585)  
 "1_75" was only coming.   
I am also attaching the ss for the same.   
  
Still the message generator issue is not solved.

---

## Comment 12

> Username: ashtum  
> Created at: 2024-02-16 06:45:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1947842114  

> I have tried again after installing boost_1_84_0 version but while the above version program gave the output as "1_75".  
  
This indicates you are including Boost version 1.75 headers, and the encountered error originates from this version.  
The newer version might have been installed in a different location; try to locate it and pass the location to the compiler:  
```BASH  
g++ -I /path/to/boost_1_82_0 ...  
```

---

## Comment 13

> Username: shivank1111  
> Created at: 2024-02-19 06:53:53 UTC  
> Updated at: 2024-02-19 06:57:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1951811573  

Hi,  
I made some changes and then while running the version.cpp file, it's giving as "Boost version: 1.83.0" but still the advanced_server.cpp is giving the same error.   
  
I had one more doubt, the versions are giving discrepancies like if I run version.cpp then it's giving as "Boost version: 1.83.0" but if I run this particular command " cat /usr/include/boost/version.hpp | grep "BOOST_LIB_VERSION" " then, this is giving "#define BOOST_LIB_VERSION "1_53" " i.e., the default boost version. I have tried several times but still not able to solve it.   
I am unable to find the reason from where it is taking that particular path.  
  
Kindly help me with the same.

---

## Comment 14

> Username: ashtum  
> Created at: 2024-02-19 07:57:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1951888897  

How did you install the new version of Boost? I think the issue lies here.

---

## Comment 15

> Username: shivank1111  
> Created at: 2024-02-19 08:25:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1951928615  

I downloaded it from Boost Library and followed the steps afterwards.

---

## Comment 16

> Username: ashtum  
> Created at: 2024-02-19 08:44:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1951958250  

> > I have tried again after installing boost_1_84_0 version but while the above version program gave the output as "1_75".  
>   
> This indicates you are including Boost version 1.75 headers, and the encountered error originates from this version. The newer version might have been installed in a different location; try to locate it and pass the location to the compiler:  
>   
> ```shell  
> g++ -I /path/to/boost_1_82_0 ...  
> ```  
  
have you tried this?

---

## Comment 17

> Username: shivank1111  
> Created at: 2024-02-19 08:55:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1951975541  

Yes, I have. Now I have the solved the version part also. In the usr/include path I changed the latest boost. Now it's showing 1.83 but that message generator issue is not solved.

---

## Comment 18

> Username: ashtum  
> Created at: 2024-02-19 08:58:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1951982177  

> Yes, I have. Now I have the solved the version part also  
How did you solve that?  
Please try to compile the source like this:  
```C++  
g++ -I /path/to/boost_1_82_0 ...  
```  
Change the path to point to the extracted boost directory.

---

## Comment 19

> Username: shivank1111  
> Created at: 2024-02-19 09:08:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1951998544  

I have tried that also.

---

## Comment 20

> Username: ashtum  
> Created at: 2024-02-19 09:11:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952004789  

What error you get with just compiling this:  
```C++  
#include <boost/beast/http/message_generator.hpp>  
  
int main(){}  
```

---

## Comment 21

> Username: shivank1111  
> Created at: 2024-02-19 09:25:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952028144  

When I ran "message_generaor.hpp" file using the command  
" g++ -std=c++11 message_generator.hpp -o message", It successfully compiled but the executable file(./message) was showing permission denied. (-bash: ./message: Permission denied

---

## Comment 22

> Username: ashtum  
> Created at: 2024-02-19 09:26:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952031461  

> When I ran "message_generaor.hpp" file using the command " g++ -std=c++11 message_generator.hpp -o message", It successfully compiled but the executable file(./message) was showing permission denied. (-bash: ./message: Permission denied  
  
What is the content of `message_generaor.hpp`?

---

## Comment 23

> Username: shivank1111  
> Created at: 2024-02-19 09:27:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952033084  

```//  
// Copyright (c) 2022 Seth Heeren (sgheeren at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
#ifndef BOOST_BEAST_HTTP_MESSAGE_GENERATOR_HPP  
#define BOOST_BEAST_HTTP_MESSAGE_GENERATOR_HPP  
  
#include <boost/beast/core/span.hpp>  
#include <boost/beast/http/message.hpp>  
#include <boost/beast/http/serializer.hpp>  
#include <memory>  
  
namespace boost {  
namespace beast {  
namespace http {  
  
/** Type-erased buffers generator for @ref http::message  
     
    Implements the BuffersGenerator concept for any concrete instance of the  
    @ref http::message template.  
     
    @ref http::message_generator takes ownership of a message on construction,  
    erasing the concrete type from the interface.  
     
    This makes it practical for use in server applications to implement request  
    handling:  
     
    @code  
    template <class Body, class Fields>  
    http::message_generator handle_request(  
        string_view doc_root,  
        http::request<Body, Fields>&& request);  
    @endcode  
     
    The @ref beast::write and @ref beast::async_write operations are provided  
    for BuffersGenerator. The @ref http::message::keep_alive property is made  
    available for use after writing the message.  
*/  
class message_generator  
{  
public:  
    using const_buffers_type = span<net::const_buffer>;  
  
    template <bool isRequest, class Body, class Fields>  
    message_generator(http::message<isRequest, Body, Fields>&&);  
  
    /// `BuffersGenerator`  
    bool is_done() const {  
        return impl_->is_done();  
    }  
  
    /// `BuffersGenerator`  
    const_buffers_type  
    prepare(error_code& ec)  
    {  
        return impl_->prepare(ec);  
    }  
  
    /// `BuffersGenerator`  
    void  
    consume(std::size_t n)  
    {  
        impl_->consume(n);  
    }  
  
    /// Returns the result of `m.keep_alive()` on the underlying message  
    bool  
    keep_alive() const noexcept  
    {  
        return impl_->keep_alive();  
    }  
  
private:  
    struct impl_base  
    {  
        virtual ~impl_base() = default;  
        virtual bool is_done() = 0;  
        virtual const_buffers_type prepare(error_code& ec) = 0;  
        virtual void consume(std::size_t n) = 0;  
        virtual bool keep_alive() const noexcept = 0;  
    };  
  
    std::unique_ptr<impl_base> impl_;  
  
    template <bool isRequest, class Body, class Fields>  
    struct generator_impl;  
};  
  
} // namespace http  
} // namespace beast  
} // namespace boost  
  
#include <boost/beast/http/impl/message_generator.hpp>  
  
#endif  
```

---

## Comment 24

> Username: ashtum  
> Created at: 2024-02-19 09:32:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952041441  

What happens when you try to compile the following source code:  
```C++  
#include <boost/beast/http/message_generator.hpp>  
  
int main(){}  
```

---

## Comment 25

> Username: shivank1111  
> Created at: 2024-02-19 09:41:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952059797  

If I compile only these 2 lines then whole lot of errors are coming.   
  
``In file included from /usr/include/c++/4.8.2/initializer_list:36:0,  
                 from /usr/local/include/boost/core/data.hpp:11,  
                 from /usr/local/include/boost/core/span.hpp:11,  
                 from /usr/local/include/boost/beast/core/span.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:12,  
                 from Message.cpp:1:  
/usr/include/c++/4.8.2/bits/c++0x_warning.h:32:2: error: #error This file requires compiler and library support for the ISO C++ 2011 standard. This support is currently experimental, and must be enabled with the -std=c++11 or -std=gnu++11 compiler options.  
 #error This file requires compiler and library support for the \  
  ^  
In file included from /usr/local/include/boost/core/span.hpp:11:0,  
                 from /usr/local/include/boost/beast/core/span.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:12,  
                 from Message.cpp:1:  
/usr/local/include/boost/core/data.hpp:17:8: error: ‘constexpr’ does not name a type  
 inline constexpr auto  
        ^  
/usr/local/include/boost/core/data.hpp:17:8: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/data.hpp:24:8: error: ‘constexpr’ does not name a type  
 inline constexpr auto  
        ^  
/usr/local/include/boost/core/data.hpp:24:8: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/data.hpp:31:8: error: ‘constexpr’ does not name a type  
 inline constexpr T*  
        ^  
/usr/local/include/boost/core/data.hpp:31:8: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/data.hpp:38:8: error: ‘constexpr’ does not name a type  
 inline constexpr const T*  
        ^  
/usr/local/include/boost/core/data.hpp:38:8: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
In file included from /usr/local/include/boost/beast/core/span.hpp:14:0,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:12,  
                 from Message.cpp:1:  
/usr/local/include/boost/core/span.hpp:18:1: error: ‘constexpr’ does not name a type  
 constexpr std::size_t dynamic_extent = static_cast<std::size_t>(-1);  
 ^  
/usr/local/include/boost/core/span.hpp:18:1: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:20:35: error: ‘dynamic_extent’ was not declared in this scope  
 template<class T, std::size_t E = dynamic_extent>  
                                   ^  
/usr/local/include/boost/core/span.hpp:27:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = std::is_convertible<U(*)[], T(*)[]>::value;  
            ^  
/usr/local/include/boost/core/span.hpp:27:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:32:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = E == boost::dynamic_extent || E == N;  
            ^  
/usr/local/include/boost/core/span.hpp:32:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:37:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = span_capacity<E, N>::value &&  
            ^  
/usr/local/include/boost/core/span.hpp:37:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:42:1: error: expected unqualified-id before ‘using’  
 using span_uncvref = typename std::remove_cv<typename  
 ^  
/usr/local/include/boost/core/span.hpp:47:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = false;  
            ^  
/usr/local/include/boost/core/span.hpp:47:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:52:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = true;  
            ^  
/usr/local/include/boost/core/span.hpp:52:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:57:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = false;  
            ^  
/usr/local/include/boost/core/span.hpp:57:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:61:22: error: ‘array’ is not a member of ‘std’  
 struct span_is_array<std::array<T, N> > {  
                      ^  
/usr/local/include/boost/core/span.hpp:61:22: error: ‘array’ is not a member of ‘std’  
/usr/local/include/boost/core/span.hpp:61:37: error: wrong number of template arguments (2, should be 1)  
 struct span_is_array<std::array<T, N> > {  
                                     ^  
/usr/local/include/boost/core/span.hpp:56:8: error: provided for ‘template<class T> struct boost::detail::span_is_array’  
 struct span_is_array {  
        ^  
/usr/local/include/boost/core/span.hpp:61:39: error: expected unqualified-id before ‘>’ token  
 struct span_is_array<std::array<T, N> > {  
                                       ^  
/usr/local/include/boost/core/span.hpp:66:1: error: expected unqualified-id before ‘using’  
 using span_ptr = decltype(boost::data(std::declval<T&>()));  
 ^  
/usr/local/include/boost/core/span.hpp:73:19: error: ‘enable_if’ in namespace ‘std’ does not name a type  
     typename std::enable_if<std::is_pointer<span_ptr<T> >::value>::type> {  
                   ^  
/usr/local/include/boost/core/span.hpp:73:28: error: expected template-argument before ‘<’ token  
     typename std::enable_if<std::is_pointer<span_ptr<T> >::value>::type> {  
                            ^  
/usr/local/include/boost/core/span.hpp:73:28: error: expected ‘>’ before ‘<’ token  
/usr/local/include/boost/core/span.hpp:73:72: error: template argument 2 is invalid  
     typename std::enable_if<std::is_pointer<span_ptr<T> >::value>::type> {  
                                                                        ^  
/usr/local/include/boost/core/span.hpp:73:74: error: expected ‘::’ before ‘{’ token  
     typename std::enable_if<std::is_pointer<span_ptr<T> >::value>::type> {  
                                                                          ^  
/usr/local/include/boost/core/span.hpp:73:74: error: expected identifier before ‘{’ token  
/usr/local/include/boost/core/span.hpp:73:74: error: qualified name does not name a class before ‘{’ token  
/usr/local/include/boost/core/span.hpp:79:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = false;  
            ^  
/usr/local/include/boost/core/span.hpp:79:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:83:42: error: ‘enable_if’ in namespace ‘std’ does not name a type  
 struct span_has_data<R, T, typename std::enable_if<span_convertible<typename  
                                          ^  
/usr/local/include/boost/core/span.hpp:83:51: error: expected template-argument before ‘<’ token  
 struct span_has_data<R, T, typename std::enable_if<span_convertible<typename  
                                                   ^  
/usr/local/include/boost/core/span.hpp:83:51: error: expected ‘>’ before ‘<’ token  
/usr/local/include/boost/core/span.hpp:84:41: error: template argument 3 is invalid  
     span_data<R>::type, T>::value>::type> {  
                                         ^  
/usr/local/include/boost/core/span.hpp:84:43: error: expected ‘::’ before ‘{’ token  
     span_data<R>::type, T>::value>::type> {  
                                           ^  
/usr/local/include/boost/core/span.hpp:84:43: error: expected identifier before ‘{’ token  
/usr/local/include/boost/core/span.hpp:84:43: error: qualified name does not name a class before ‘{’ token  
/usr/local/include/boost/core/span.hpp:90:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = false;  
            ^  
/usr/local/include/boost/core/span.hpp:90:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:95:10: error: ‘enable_if’ in namespace ‘std’ does not name a type  
     std::enable_if<std::is_convertible<decltype(std::declval<R&>().size()),  
          ^  
/usr/local/include/boost/core/span.hpp:95:19: error: expected template-argument before ‘<’ token  
     std::enable_if<std::is_convertible<decltype(std::declval<R&>().size()),  
                   ^  
/usr/local/include/boost/core/span.hpp:95:19: error: expected ‘>’ before ‘<’ token  
/usr/local/include/boost/core/span.hpp:96:35: error: template argument 2 is invalid  
         std::size_t>::value>::type> {  
                                   ^  
/usr/local/include/boost/core/span.hpp:96:37: error: expected ‘::’ before ‘{’ token  
         std::size_t>::value>::type> {  
                                     ^  
/usr/local/include/boost/core/span.hpp:96:37: error: expected identifier before ‘{’ token  
/usr/local/include/boost/core/span.hpp:96:37: error: qualified name does not name a class before ‘{’ token  
/usr/local/include/boost/core/span.hpp:102:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = (std::is_const<T>::value ||  
            ^  
/usr/local/include/boost/core/span.hpp:102:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:113:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = E == boost::dynamic_extent ||  
            ^  
/usr/local/include/boost/core/span.hpp:113:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:119:12: error: ‘constexpr’ does not name a type  
     static constexpr bool value = (N == boost::dynamic_extent ||  
            ^  
/usr/local/include/boost/core/span.hpp:119:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:125:12: error: ‘constexpr’ does not name a type  
     static constexpr std::size_t value = E == boost::dynamic_extent ?  
            ^  
/usr/local/include/boost/core/span.hpp:125:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:131:5: error: ‘constexpr’ does not name a type  
     constexpr span_store(T* p_, std::size_t) noexcept  
     ^  
/usr/local/include/boost/core/span.hpp:131:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:133:12: error: ‘constexpr’ does not name a type  
     static constexpr std::size_t n = E;  
            ^  
/usr/local/include/boost/core/span.hpp:133:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:138:22: error: ‘dynamic_extent’ is not a member of ‘boost’  
 struct span_store<T, boost::dynamic_extent> {  
                      ^  
/usr/local/include/boost/core/span.hpp:138:22: error: ‘dynamic_extent’ is not a member of ‘boost’  
/usr/local/include/boost/core/span.hpp:138:43: error: template argument 2 is invalid  
 struct span_store<T, boost::dynamic_extent> {  
                                           ^  
/usr/local/include/boost/core/span.hpp:148:12: error: ‘constexpr’ does not name a type  
     static constexpr std::size_t value = sizeof(T) * E;  
            ^  
/usr/local/include/boost/core/span.hpp:148:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:152:22: error: ‘dynamic_extent’ is not a member of ‘boost’  
 struct span_bytes<T, boost::dynamic_extent> {  
                      ^  
/usr/local/include/boost/core/span.hpp:152:22: error: ‘dynamic_extent’ is not a member of ‘boost’  
/usr/local/include/boost/core/span.hpp:152:43: error: template argument 2 is invalid  
 struct span_bytes<T, boost::dynamic_extent> {  
                                           ^  
/usr/local/include/boost/core/span.hpp:162:27: error: ‘remove_cv’ in namespace ‘std’ does not name a type  
     typedef typename std::remove_cv<T>::type value_type;  
                           ^  
/usr/local/include/boost/core/span.hpp:162:36: error: expected unqualified-id before ‘<’ token  
     typedef typename std::remove_cv<T>::type value_type;  
                                    ^  
/usr/local/include/boost/core/span.hpp:174:12: error: ‘constexpr’ does not name a type  
     static constexpr std::size_t extent = E;  
            ^  
/usr/local/include/boost/core/span.hpp:174:12: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:177:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<N == dynamic_extent || N == 0, int>::type = 0>  
                       ^  
/usr/local/include/boost/core/span.hpp:177:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<N == dynamic_extent || N == 0, int>::type = 0>  
                                ^  
/usr/local/include/boost/core/span.hpp:178:5: error: ‘constexpr’ does not name a type  
     constexpr span() noexcept  
     ^  
/usr/local/include/boost/core/span.hpp:178:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:182:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<E == dynamic_extent &&  
                       ^  
/usr/local/include/boost/core/span.hpp:182:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<E == dynamic_extent &&  
                                ^  
/usr/local/include/boost/core/span.hpp:184:5: error: ‘constexpr’ does not name a type  
     constexpr span(I* f, size_type c)  
     ^  
/usr/local/include/boost/core/span.hpp:184:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:188:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<E != dynamic_extent &&  
                       ^  
/usr/local/include/boost/core/span.hpp:188:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<E != dynamic_extent &&  
                                ^  
/usr/local/include/boost/core/span.hpp:190:14: error: ‘constexpr’ does not name a type  
     explicit constexpr span(I* f, size_type c)  
              ^  
/usr/local/include/boost/core/span.hpp:190:14: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:194:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<E == dynamic_extent &&  
                       ^  
/usr/local/include/boost/core/span.hpp:194:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<E == dynamic_extent &&  
                                ^  
/usr/local/include/boost/core/span.hpp:196:5: error: ‘constexpr’ does not name a type  
     constexpr span(I* f, L* l)  
     ^  
/usr/local/include/boost/core/span.hpp:196:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:200:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<E != dynamic_extent &&  
                       ^  
/usr/local/include/boost/core/span.hpp:200:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<E != dynamic_extent &&  
                                ^  
/usr/local/include/boost/core/span.hpp:202:14: error: ‘constexpr’ does not name a type  
     explicit constexpr span(I* f, L* l)  
              ^  
/usr/local/include/boost/core/span.hpp:202:14: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:206:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<detail::span_capacity<E, N>::value,  
                       ^  
/usr/local/include/boost/core/span.hpp:206:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<detail::span_capacity<E, N>::value,  
                                ^  
/usr/local/include/boost/core/span.hpp:208:5: error: ‘constexpr’ does not name a type  
     constexpr span(typename std::enable_if<true, T>::type (&a)[N]) noexcept  
     ^  
/usr/local/include/boost/core/span.hpp:208:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:212:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<detail::span_compatible<T, E, U, N>::value,  
                       ^  
/usr/local/include/boost/core/span.hpp:212:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<detail::span_compatible<T, E, U, N>::value,  
                                ^  
/usr/local/include/boost/core/span.hpp:214:5: error: ‘constexpr’ does not name a type  
     constexpr span(std::array<U, N>& a) noexcept  
     ^  
/usr/local/include/boost/core/span.hpp:214:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:218:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<detail::span_compatible<T, E, const U,  
                       ^  
/usr/local/include/boost/core/span.hpp:218:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<detail::span_compatible<T, E, const U,  
                                ^  
/usr/local/include/boost/core/span.hpp:220:5: error: ‘constexpr’ does not name a type  
     constexpr span(const std::array<U, N>& a) noexcept  
     ^  
/usr/local/include/boost/core/span.hpp:220:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:224:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<E == dynamic_extent &&  
                       ^  
/usr/local/include/boost/core/span.hpp:224:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<E == dynamic_extent &&  
                                ^  
/usr/local/include/boost/core/span.hpp:226:5: error: ‘constexpr’ does not name a type  
     constexpr span(R&& r) noexcept(noexcept(boost::data(r)) &&  
     ^  
/usr/local/include/boost/core/span.hpp:226:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:231:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<E != dynamic_extent &&  
                       ^  
/usr/local/include/boost/core/span.hpp:231:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<E != dynamic_extent &&  
                                ^  
/usr/local/include/boost/core/span.hpp:233:14: error: ‘constexpr’ does not name a type  
     explicit constexpr span(R&& r) noexcept(noexcept(boost::data(r)) &&  
              ^  
/usr/local/include/boost/core/span.hpp:233:14: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:238:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<detail::span_implicit<E, N>::value &&  
                       ^  
/usr/local/include/boost/core/span.hpp:238:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<detail::span_implicit<E, N>::value &&  
                                ^  
/usr/local/include/boost/core/span.hpp:240:5: error: ‘constexpr’ does not name a type  
     constexpr span(const span<U, N>& s) noexcept  
     ^  
/usr/local/include/boost/core/span.hpp:240:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:244:23: error: ‘enable_if’ in namespace ‘std’ does not name a type  
         typename std::enable_if<!detail::span_implicit<E, N>::value &&  
                       ^  
/usr/local/include/boost/core/span.hpp:244:32: error: expected ‘>’ before ‘<’ token  
         typename std::enable_if<!detail::span_implicit<E, N>::value &&  
                                ^  
/usr/local/include/boost/core/span.hpp:246:14: error: ‘constexpr’ does not name a type  
     explicit constexpr span(const span<U, N>& s) noexcept  
              ^  
/usr/local/include/boost/core/span.hpp:246:14: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:250:5: error: ‘constexpr’ does not name a type  
     constexpr span<T, C> first() const {  
     ^  
/usr/local/include/boost/core/span.hpp:250:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:256:5: error: ‘constexpr’ does not name a type  
     constexpr span<T, C> last() const {  
     ^  
/usr/local/include/boost/core/span.hpp:256:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:261:45: error: ‘dynamic_extent’ was not declared in this scope  
     template<std::size_t O, std::size_t C = dynamic_extent>  
                                             ^  
/usr/local/include/boost/core/span.hpp:262:5: error: ‘constexpr’ does not name a type  
     constexpr typename std::enable_if<C == dynamic_extent,  
     ^  
/usr/local/include/boost/core/span.hpp:262:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:268:45: error: ‘dynamic_extent’ was not declared in this scope  
     template<std::size_t O, std::size_t C = dynamic_extent>  
                                             ^  
/usr/local/include/boost/core/span.hpp:269:5: error: ‘constexpr’ does not name a type  
     constexpr typename std::enable_if<C != dynamic_extent,  
     ^  
/usr/local/include/boost/core/span.hpp:269:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:276:5: error: ‘constexpr’ does not name a type  
     constexpr span<T, dynamic_extent> first(size_type c) const {  
     ^  
/usr/local/include/boost/core/span.hpp:276:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:280:5: error: ‘constexpr’ does not name a type  
     constexpr span<T, dynamic_extent> last(size_type c) const {  
     ^  
/usr/local/include/boost/core/span.hpp:280:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:284:5: error: ‘constexpr’ does not name a type  
     constexpr span<T, dynamic_extent> subspan(size_type o,  
     ^  
/usr/local/include/boost/core/span.hpp:284:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:290:5: error: ‘constexpr’ does not name a type  
     constexpr size_type size() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:290:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:294:5: error: ‘constexpr’ does not name a type  
     constexpr size_type size_bytes() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:294:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:298:5: error: ‘constexpr’ does not name a type  
     constexpr bool empty() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:298:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:302:5: error: ‘constexpr’ does not name a type  
     constexpr reference operator[](size_type i) const {  
     ^  
/usr/local/include/boost/core/span.hpp:302:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:306:5: error: ‘constexpr’ does not name a type  
     constexpr reference front() const {  
     ^  
/usr/local/include/boost/core/span.hpp:306:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:310:5: error: ‘constexpr’ does not name a type  
     constexpr reference back() const {  
     ^  
/usr/local/include/boost/core/span.hpp:310:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:314:5: error: ‘constexpr’ does not name a type  
     constexpr pointer data() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:314:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:318:5: error: ‘constexpr’ does not name a type  
     constexpr iterator begin() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:318:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:322:5: error: ‘constexpr’ does not name a type  
     constexpr iterator end() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:322:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:326:5: error: ‘constexpr’ does not name a type  
     constexpr reverse_iterator rbegin() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:326:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:330:5: error: ‘constexpr’ does not name a type  
     constexpr reverse_iterator rend() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:330:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:334:5: error: ‘constexpr’ does not name a type  
     constexpr const_iterator cbegin() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:334:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:338:5: error: ‘constexpr’ does not name a type  
     constexpr const_iterator cend() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:338:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:342:5: error: ‘constexpr’ does not name a type  
     constexpr const_reverse_iterator crbegin() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:342:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:346:5: error: ‘constexpr’ does not name a type  
     constexpr const_reverse_iterator crend() const noexcept {  
     ^  
/usr/local/include/boost/core/span.hpp:346:5: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
/usr/local/include/boost/core/span.hpp:355:1: error: ‘constexpr’ does not name a type  
 constexpr std::size_t span<T, E>::extent;  
 ^  
/usr/local/include/boost/core/span.hpp:355:1: note: C++11 ‘constexpr’ only available with -std=c++11 or -std=gnu++11  
In file included from /usr/local/include/boost/beast/http/message_generator.hpp:12:0,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/span.hpp:19:35: error: ‘dynamic_extent’ is not a member of ‘boost’  
 template<class T, std::size_t E = boost::dynamic_extent>  
                                   ^  
/usr/local/include/boost/beast/core/span.hpp:20:1: error: expected unqualified-id before ‘using’  
 using span = boost::span<T, E>;  
 ^  
In file included from /usr/local/include/boost/beast/core/string.hpp:14:0,  
                 from /usr/local/include/boost/beast/http/fields.hpp:14,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/string_type.hpp:25:7: error: expected nested-name-specifier before ‘string_view’  
 using string_view = boost::core::string_view;  
       ^  
/usr/local/include/boost/beast/core/string_type.hpp:25:7: error: ‘string_view’ has not been declared  
/usr/local/include/boost/beast/core/string_type.hpp:25:19: error: expected ‘;’ before ‘=’ token  
 using string_view = boost::core::string_view;  
                   ^  
/usr/local/include/boost/beast/core/string_type.hpp:25:19: error: expected unqualified-id before ‘=’ token  
/usr/local/include/boost/beast/core/string_type.hpp:29:1: error: expected unqualified-id before ‘using’  
 using basic_string_view =  
 ^  
/usr/local/include/boost/beast/core/string_type.hpp:33:8: error: ‘string_view’ does not name a type  
 inline string_view  
        ^  
In file included from /usr/local/include/boost/beast/http/fields.hpp:14:0,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/string.hpp:30:12: error: ‘boost::beast::iequals’ declared as an ‘inline’ variable  
     beast::string_view lhs,  
            ^  
/usr/local/include/boost/beast/core/string.hpp:30:5: error: ‘string_view’ is not a member of ‘boost::beast’  
     beast::string_view lhs,  
     ^  
/usr/local/include/boost/beast/core/string.hpp:30:5: note: suggested alternative:  
In file included from /usr/local/include/boost/beast/core/string_type.hpp:15:0,  
                 from /usr/local/include/boost/beast/core/string.hpp:14,  
                 from /usr/local/include/boost/beast/http/fields.hpp:14,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/core/detail/string_view.hpp:1229:33: note:   ‘boost::core::string_view’  
 typedef basic_string_view<char> string_view;  
                                 ^  
In file included from /usr/local/include/boost/beast/http/fields.hpp:14:0,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/string.hpp:31:5: error: ‘string_view’ is not a member of ‘boost::beast’  
     beast::string_view rhs);  
     ^  
/usr/local/include/boost/beast/core/string.hpp:31:5: note: suggested alternative:  
In file included from /usr/local/include/boost/beast/core/string_type.hpp:15:0,  
                 from /usr/local/include/boost/beast/core/string.hpp:14,  
                 from /usr/local/include/boost/beast/http/fields.hpp:14,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/core/detail/string_view.hpp:1229:33: note:   ‘boost::core::string_view’  
 typedef basic_string_view<char> string_view;  
                                 ^  
In file included from /usr/local/include/boost/beast/http/fields.hpp:14:0,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/string.hpp:31:27: error: expression list treated as compound expression in initializer [-fpermissive]  
     beast::string_view rhs);  
                           ^  
/usr/local/include/boost/beast/core/string.hpp:46:9: error: ‘string_view’ has not been declared  
         string_view lhs,  
         ^  
/usr/local/include/boost/beast/core/string.hpp:47:9: error: ‘string_view’ has not been declared  
         string_view rhs) const;  
         ^  
/usr/local/include/boost/beast/core/string.hpp:49:11: error: expected nested-name-specifier before ‘is_transparent’  
     using is_transparent = void;  
           ^  
/usr/local/include/boost/beast/core/string.hpp:49:11: error: using-declaration for non-member at class scope  
/usr/local/include/boost/beast/core/string.hpp:49:26: error: expected ‘;’ before ‘=’ token  
     using is_transparent = void;  
                          ^  
/usr/local/include/boost/beast/core/string.hpp:49:26: error: expected unqualified-id before ‘=’ token  
/usr/local/include/boost/beast/core/string.hpp:64:9: error: ‘string_view’ has not been declared  
         string_view lhs,  
         ^  
/usr/local/include/boost/beast/core/string.hpp:65:9: error: ‘string_view’ has not been declared  
         string_view rhs) const  
         ^  
/usr/local/include/boost/beast/core/string.hpp:70:11: error: expected nested-name-specifier before ‘is_transparent’  
     using is_transparent = void;  
           ^  
/usr/local/include/boost/beast/core/string.hpp:70:11: error: using-declaration for non-member at class scope  
/usr/local/include/boost/beast/core/string.hpp:70:26: error: expected ‘;’ before ‘=’ token  
     using is_transparent = void;  
                          ^  
/usr/local/include/boost/beast/core/string.hpp:70:26: error: expected unqualified-id before ‘=’ token  
/usr/local/include/boost/beast/core/string.hpp: In member function ‘bool boost::beast::iequal::operator()(int, int) const’:  
/usr/local/include/boost/beast/core/string.hpp:67:32: error: ‘boost::beast::iequals’ cannot be used as a function  
         return iequals(lhs, rhs);  
                                ^  
In file included from /usr/local/include/boost/beast/core/impl/string.ipp:14:0,  
                 from /usr/local/include/boost/beast/core/string.hpp:77,  
                 from /usr/local/include/boost/beast/http/fields.hpp:14,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/detail/string.hpp: At global scope:  
/usr/local/include/boost/beast/core/detail/string.hpp:25:1: error: ‘string_view’ does not name a type  
 string_view  
 ^  
In file included from /usr/local/include/boost/beast/core/string.hpp:77:0,  
                 from /usr/local/include/boost/beast/http/fields.hpp:14,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/impl/string.ipp:23:12: error: redefinition of ‘bool boost::beast::iequals’  
     beast::string_view lhs,  
            ^  
In file included from /usr/local/include/boost/beast/http/fields.hpp:14:0,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/string.hpp:29:1: error: ‘bool boost::beast::iequals’ previously defined here  
 iequals(  
 ^  
In file included from /usr/local/include/boost/beast/core/string.hpp:77:0,  
                 from /usr/local/include/boost/beast/http/fields.hpp:14,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/impl/string.ipp:23:5: error: ‘string_view’ is not a member of ‘boost::beast’  
     beast::string_view lhs,  
     ^  
/usr/local/include/boost/beast/core/impl/string.ipp:23:5: note: suggested alternative:  
In file included from /usr/local/include/boost/beast/core/string_type.hpp:15:0,  
                 from /usr/local/include/boost/beast/core/string.hpp:14,  
                 from /usr/local/include/boost/beast/http/fields.hpp:14,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/core/detail/string_view.hpp:1229:33: note:   ‘boost::core::string_view’  
 typedef basic_string_view<char> string_view;  
                                 ^  
In file included from /usr/local/include/boost/beast/core/string.hpp:77:0,  
                 from /usr/local/include/boost/beast/http/fields.hpp:14,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/beast/core/impl/string.ipp:24:5: error: ‘string_view’ is not a member of ‘boost::beast’  
     beast::string_view rhs)  
     ^  
/usr/local/include/boost/beast/core/impl/string.ipp:24:5: note: suggested alternative:  
In file included from /usr/local/include/boost/beast/core/string_type.hpp:15:0,  
                 from /usr/local/include/boost/beast/core/string.hpp:14,  
                 from /usr/local/include/boost/beast/http/fields.hpp:14,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/core/detail/string_view.hpp:1229:33: note:   ‘boost::core::string_view’  
 typedef basic_string_view<char> string_view;  
                                 ^  
In file included from /usr/local/include/boost/container/allocator_traits.hpp:27:0,  
                 from /usr/local/include/boost/beast/core/detail/allocator.hpp:15,  
                 from /usr/local/include/boost/beast/http/fields.hpp:15,  
                 from /usr/local/include/boost/beast/http/message.hpp:14,  
                 from /usr/local/include/boost/beast/http/message_generator.hpp:13,  
                 from Message.cpp:1:  
/usr/local/include/boost/container/detail/config_begin.hpp:55:28: error: expected ‘}’ before end of line  
 #pragma GCC diagnostic push  
                            ^  
/usr/local/include/boost/container/detail/config_begin.hpp:55:28: error: expected ‘}’ before end of line  
/usr/local/include/boost/container/detail/config_begin.hpp:55:28: error: expected declaration before end of line  
[root@localhost evsnotify]#  
``

---

## Comment 26

> Username: ashtum  
> Created at: 2024-02-19 09:43:42 UTC  
> Updated at: 2024-02-19 09:44:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952065120  

Please use `-std=c++11` switch in the compile command.

---

## Comment 27

> Username: shivank1111  
> Created at: 2024-02-19 09:58:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952092159  

It's compiling.  
  
Attaching the SS.  
![compile](https://github.com/boostorg/beast/assets/160120467/d8aae7fc-30f3-452b-b6f4-97629ec53ccf)

---

## Comment 28

> Username: shivank1111  
> Created at: 2024-02-19 10:02:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952098772  

Still the error is coming. I am using this command line to run:   
 g++ -std=c++11 -I/home/evsnotify/boost_1_84_0/boost  advanced_server.cpp  -o advance  -lpthread -lboost_system -lboost_filesystem -lboost_coroutine

---

## Comment 29

> Username: ashtum  
> Created at: 2024-02-19 10:07:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952109250  

Please provide the generated error message in a code block.

---

## Comment 30

> Username: shivank1111  
> Created at: 2024-02-19 10:08:51 UTC  
> Updated at: 2024-02-19 10:09:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952110742  

Sure. I made cross checked at the paths this is showing error nut there I haven't changed anything.   
  
``advanced_server.cpp: In instantiation of ‘boost::beast::http::message_generator handle_request(boost::beast::string_view, boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >&&) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; boost::beast::string_view = boost::core::basic_string_view<char>; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]’:  
advanced_server.cpp:412:66:   required from here  
advanced_server.cpp:194:16: error: cannot bind ‘boost::beast::http::response<boost::beast::http::empty_body> {aka boost::beast::http::message<false, boost::beast::http::empty_body, boost::beast::http::basic_fields<std::allocator<char> > >}’ lvalue to ‘boost::beast::http::message<false, boost::beast::http::empty_body, boost::beast::http::basic_fields<std::allocator<char> > >&&’  
         return res;  
                ^  
In file included from /usr/local/include/boost/beast/http/message_generator.hpp:97:0,  
                 from /usr/local/include/boost/beast/http.hpp:26,  
                 from advanced_server.cpp:17:  
/usr/local/include/boost/beast/http/impl/message_generator.hpp:22:1: error:   initializing argument 1 of ‘boost::beast::http::message_generator::message_generator(boost::beast::http::message<isRequest, Body, Fields>&&) [with bool isRequest = false; Body = boost::beast::http::empty_body; Fields = boost::beast::http::basic_fields<std::allocator<char> >]’  
 message_generator::message_generator(  
 ^  
advanced_server.cpp:206:12: error: cannot bind ‘boost::beast::http::response<boost::beast::http::basic_file_body<boost::beast::file_posix> > {aka boost::beast::http::message<false, boost::beast::http::basic_file_body<boost::beast::file_posix>, boost::beast::http::basic_fields<std::allocator<char> > >}’ lvalue to ‘boost::beast::http::message<false, boost::beast::http::basic_file_body<boost::beast::file_posix>, boost::beast::http::basic_fields<std::allocator<char> > >&&’  
     return res;  
            ^  
In file included from /usr/local/include/boost/beast/http/message_generator.hpp:97:0,  
                 from /usr/local/include/boost/beast/http.hpp:26,  
                 from advanced_server.cpp:17:  
/usr/local/include/boost/beast/http/impl/message_generator.hpp:22:1: error:   initializing argument 1 of ‘boost::beast::http::message_generator::message_generator(boost::beast::http::message<isRequest, Body, Fields>&&) [with bool isRequest = false; Body = boost::beast::http::basic_file_body<boost::beast::file_posix>; Fields = boost::beast::http::basic_fields<std::allocator<char> >]’  
 message_generator::message_generator(  
``

---

## Comment 31

> Username: ashtum  
> Created at: 2024-02-19 10:12:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952117721  

Please provide the full error message. What is you compiler version?

---

## Comment 32

> Username: shivank1111  
> Created at: 2024-02-19 10:14:50 UTC  
> Updated at: 2024-02-19 10:15:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952121283  

The above error was the whole error. I have provided the ss of  version of my compiler and also the error.   
  
  
![Complete_error](https://github.com/boostorg/beast/assets/160120467/0be289d2-ef5d-46a1-9d12-24ff86c65012)  
![compiler_version](https://github.com/boostorg/beast/assets/160120467/366cc29d-1a75-40d3-87ea-98d333fd9d75)

---

## Comment 33

> Username: ashtum  
> Created at: 2024-02-19 10:19:34 UTC  
> Updated at: 2024-02-19 10:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952131813  

> In the usr/include path I changed the latest boost. Now it's showing 1.83  
  
Could you please explain how did you do that?  
  
In the screenshot, I see you are passing `-I/home/evsnotify/boost_1_84_0/boost` which is wrong it should be  `-I/home/evsnotify/boost_1_84_0` or `-isystem /home/evsnotify/boost_1_84_0`

---

## Comment 34

> Username: shivank1111  
> Created at: 2024-02-19 10:23:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952138220  

I had done with Boost_1.83 before but since it was not happening I tried with Boost_1.84 version.   
  
I am attaching the ss, same error came with Boost_1.83.  
  
  
![Complete_error_1 83](https://github.com/boostorg/beast/assets/160120467/f61b357c-b3cc-432a-ab96-ba09fce68178)

---

## Comment 35

> Username: shivank1111  
> Created at: 2024-02-19 10:26:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952143781  

SS of the version changes.  
![version_1 83](https://github.com/boostorg/beast/assets/160120467/5de2eaac-80a2-45a6-a4e2-ea61d67c32d3)

---

## Comment 36

> Username: shivank1111  
> Created at: 2024-02-19 10:28:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952146863  

My genuine doubt is that if I am giving the -I/ path to boost/ then does it matter that my system has any other boost version as default.

---

## Comment 37

> Username: ashtum  
> Created at: 2024-02-19 10:35:08 UTC  
> Updated at: 2024-02-19 10:47:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952159810  

The error message shows the header files are included from `/usr/local/...` which is not what you want if you are passing `-I/home/evsnotify/boost_1_84_0`. (note you are passing an extra /boost to the path which should be removed).  
  
Anyways, I think after updating Boost, the error message that you are facing now is because of using an old GCC version: https://godbolt.org/z/9jEjGaG7r  
Please try to update GCC to at least version 5.

---

## Comment 38

> Username: ashtum  
> Created at: 2024-02-19 10:52:19 UTC  
> Updated at: 2024-02-19 11:02:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952190983  

If it is not possible to upgrade your compiler, you can replace these lines:  
```C++  
    return res;  
```  
With:  
```C++  
    return std::move(res);  
```  
I think this might resolve this specific issue.

---

## Comment 39

> Username: shivank1111  
> Created at: 2024-02-19 12:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952330006  

Hi, Thankyou after installing the latest version of GCC Compiler, half problem was solved and after that when I ran a command to enable the scl then finally it compiled.  
But now when I am trying to connect through an online application of Rested (giving the same inputs as when I gave in the command line side, it's not responding.  
Can you please help me with the same.

---

## Comment 40

> Username: ashtum  
> Created at: 2024-02-19 14:36:18 UTC  
> Updated at: 2024-02-19 14:37:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1952579862  

It is hard to guess what might be wrong. try to debug each step in the application and see if you are accepting any connections or not.

---

## Comment 41

> Username: shivank1111  
> Created at: 2024-02-20 10:25:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2822#issuecomment-1953905256  

Thankyou so much for the help.
