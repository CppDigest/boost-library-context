# #2938 - http async server example read POST request very slow [Closed]

> Username: awangzy  
> Created at: 2024-10-09 16:08:52 UTC  
> Updated at: 2024-10-11 06:08:55 UTC  
> Closed at: 2024-10-11 06:08:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2938  

### My Problem  
Given the official http async server example(code shown below), I tried curl the server. I found that if curl with a bit longer data(curl command shown below), the `async_read()` cost long time, there is a large time gap between the `do_read()` and `on_read()`, about 1 second, which is very strange.  
  
### Relevant information  
Boost 1.72.0  
BOOST_BEAST_VERSION 277  
g++ (GCC) 10.2.1  
Kernel: Linux 5.10.134-16.3.al8.x86_64  
Architecture: x86-64  
  
### Steps necessary to reproduce the problem  
**using the official http async server example code(only add two log in do_read() and on_read())** https://www.boost.org/doc/libs/1_72_0/libs/beast/example/http/server/async/http_server_async.cpp  
**compile command:** g++ -std=c++17 -o httpServer httpServer.cpp -lboost_system -lboost_thread -lpthread -lglog  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/config.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
#include <glog/logging.h>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
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
  
// This function produces an HTTP response for the given  
// request. The type of the response object depends on the  
// contents of the request, so the interface requires the  
// caller to pass a generic lambda for receiving the response.  
template<  
    class Body, class Allocator,  
    class Send>  
void  
handle_request(  
    beast::string_view doc_root,  
    http::request<Body, http::basic_fields<Allocator>>&& req,  
    Send&& send)  
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
        return send(bad_request("Unknown HTTP-method"));  
  
    // Request path must be absolute and not contain "..".  
    if( req.target().empty() ||  
        req.target()[0] != '/' ||  
        req.target().find("..") != beast::string_view::npos)  
        return send(bad_request("Illegal request-target"));  
  
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
        return send(not_found(req.target()));  
  
    // Handle an unknown error  
    if(ec)  
        return send(server_error(ec.message()));  
  
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
        return send(std::move(res));  
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
    return send(std::move(res));  
}  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Handles an HTTP server connection  
class session : public std::enable_shared_from_this<session>  
{  
    // This is the C++11 equivalent of a generic lambda.  
    // The function object is used to send an HTTP message.  
    struct send_lambda  
    {  
        session& self_;  
  
        explicit  
        send_lambda(session& self)  
            : self_(self)  
        {  
        }  
  
        template<bool isRequest, class Body, class Fields>  
        void  
        operator()(http::message<isRequest, Body, Fields>&& msg) const  
        {  
            // The lifetime of the message has to extend  
            // for the duration of the async operation so  
            // we use a shared_ptr to manage it.  
            auto sp = std::make_shared<  
                http::message<isRequest, Body, Fields>>(std::move(msg));  
  
            // Store a type-erased version of the shared  
            // pointer in the class to keep it alive.  
            self_.res_ = sp;  
  
            // Write the response  
            http::async_write(  
                self_.stream_,  
                *sp,  
                beast::bind_front_handler(  
                    &session::on_write,  
                    self_.shared_from_this(),  
                    sp->need_eof()));  
        }  
    };  
  
    beast::tcp_stream stream_;  
    beast::flat_buffer buffer_;  
    std::shared_ptr<std::string const> doc_root_;  
    http::request<http::string_body> req_;  
    std::shared_ptr<void> res_;  
    send_lambda lambda_;  
  
public:  
    // Take ownership of the stream  
    session(  
        tcp::socket&& socket,  
        std::shared_ptr<std::string const> const& doc_root)  
        : stream_(std::move(socket))  
        , doc_root_(doc_root)  
        , lambda_(*this)  
    {  
        stream_.socket().set_option(boost::asio::ip::tcp::no_delay(true));  
    }  
  
    // Start the asynchronous operation  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(stream_.get_executor(),  
                      beast::bind_front_handler(  
                          &session::do_read,  
                          shared_from_this()));  
    }  
  
    void  
    do_read()  
    {  
        LOG(INFO) << "do_read";  
        // Make the request empty before reading,  
        // otherwise the operation behavior is undefined.  
        req_ = {};  
  
        // Set the timeout.  
        stream_.expires_after(std::chrono::seconds(30));  
  
        // Read a request  
        http::async_read(stream_, buffer_, req_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        LOG(INFO) << "on_read "<< bytes_transferred;  
        boost::ignore_unused(bytes_transferred);  
  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            return do_close();  
  
        if(ec)  
            return fail(ec, "read");  
  
        // Send the response  
        handle_request(*doc_root_, std::move(req_), lambda_);  
    }  
  
    void  
    on_write(  
        bool close,  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        if(close)  
        {  
            // This means we should close the connection, usually because  
            // the response indicated the "Connection: close" semantic.  
            return do_close();  
        }  
  
        // We're done with the response so delete it  
        res_ = nullptr;  
  
        // Read another request  
        do_read();  
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
            std::make_shared<session>(  
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
            "Usage: http-server-async <address> <port> <doc_root> <threads>\n" <<  
            "Example:\n" <<  
            "    http-server-async 0.0.0.0 8080 . 1\n";  
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
  
**start server**:   
./httpServer  0.0.0.0 8080 . 1  
**client post command**:  
curl  -X POST --data "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"      localhost:8080  
**on the server side:**  
[root@XXX beast_server]# ./httpServer  0.0.0.0 8080 . 1  
WARNING: Logging before InitGoogleLogging() is written to STDERR  
I1009 23:52:17.548391 3851554 httpServer.cpp:292] do_read  
I1009 23:52:18.549459 3851554 httpServer.cpp:312] on_read 1201  
I1009 23:52:18.549533 3851554 httpServer.cpp:292] do_read  
I1009 23:52:18.549563 3851554 httpServer.cpp:312] on_read 0  
**_there is a one-second gap between the` do_read()` log and `on_read()`log, only receiving 1201 bytes request. This is very slow._**  
  
My question is Why this happens and How to make it fast? Thank you very much.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-10-09 16:24:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2938#issuecomment-2402777268  

There might be an issue with your platform, as I am unable to reproduce the problem:  
```  
I1009 16:23:45.504467 199712 http_server_async.cpp:277] do_read  
I1009 16:23:45.504506 199712 http_server_async.cpp:297] on_read 1178  
I1009 16:23:45.504539 199712 http_server_async.cpp:277] do_read  
I1009 16:23:45.504644 199712 http_server_async.cpp:297] on_read 0  
```

---

## Comment 2

> Username: sehe  
> Created at: 2024-10-09 21:10:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2938#issuecomment-2403435810  

Reminds me a lot of the infamous war stories where there's a MTU mismatch on a hop along the route.  
  
 - https://blog.osnexus.com/2024/01/31/the-problem-with-jumbo-frames/  
 - https://support.citrix.com/s/article/CTX282710-performance-issues-and-slowness-due-to-jumbo-frames?language=en_US  
  
But in practice, any faulty device along the way can really wreak havoc:  
  
https://mina.naguib.ca/blog/2012/10/22/the-little-ssh-that-sometimes-couldnt.html  
  
In other words, eliminate all possible points of failure to see what parties are involved in the observed behavior.

---

## Comment 3

> Username: awangzy  
> Created at: 2024-10-10 00:45:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2938#issuecomment-2403679148  

> There might be an issue with your platform, as I am unable to reproduce the problem:  
>   
> ```  
> I1009 16:23:45.504467 199712 http_server_async.cpp:277] do_read  
> I1009 16:23:45.504506 199712 http_server_async.cpp:297] on_read 1178  
> I1009 16:23:45.504539 199712 http_server_async.cpp:277] do_read  
> I1009 16:23:45.504644 199712 http_server_async.cpp:297] on_read 0  
> ```  
  
Could you try to curl the server with longer data? As I see On your server side only 1178 bytes transferred(1201 bytes for me), maybe with more bytes transferred(for me, > 1200), the problem can be reproduced.

---

## Comment 4

> Username: awangzy  
> Created at: 2024-10-10 02:28:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2938#issuecomment-2403800241  

> Reminds me a lot of the infamous war stories where there's a MTU mismatch on a hop along the route.  
>   
> * https://blog.osnexus.com/2024/01/31/the-problem-with-jumbo-frames/  
> * https://support.citrix.com/s/article/CTX282710-performance-issues-and-slowness-due-to-jumbo-frames?language=en_US  
>   
> But in practice, any faulty device along the way can really wreak havoc:  
>   
> https://mina.naguib.ca/blog/2012/10/22/the-little-ssh-that-sometimes-couldnt.html  
>   
> In other words, eliminate all possible points of failure to see what parties are involved in the observed behavior.  
  
My client and server are both on the same machine, client curl the localhost to connect server. So I think there is no MTU mismatch along the route?

---

## Comment 5

> Username: awangzy  
> Created at: 2024-10-10 03:07:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2938#issuecomment-2403847351  

seem likes problem with curl, I tried **_wget command_**:  
  
> wget --post-data "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" localhost:8080  
  
server reads fast:  
  
> I1010 11:03:44.322244 3871581 httpServer.cpp:292] do_read  
> I1010 11:03:44.322391 3871581 httpServer.cpp:312] on_read 1242  
> I1010 11:03:44.322453 3871581 httpServer.cpp:292] do_read  
> I1010 11:03:44.322701 3871581 httpServer.cpp:312] on_read 0

---

## Comment 6

> Username: awangzy  
> Created at: 2024-10-10 06:08:22 UTC  
> Updated at: 2024-10-10 06:12:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2938#issuecomment-2404116307  

@ashtum @sehe   
Seems like problem with Expect: 100-continue. The server doesn't support Expect: 100-continue(see https://github.com/curl/curl/issues/1284#issuecomment-282088514), which makes curl wait a whole second before it sends its payload.  
  
Thank you for your efforts!

---

## Comment 7

> Username: ashtum  
> Created at: 2024-10-10 06:56:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2938#issuecomment-2404201213  

> @ashtum @sehe Seems like problem with Expect: 100-continue. The server doesn't support Expect: 100-continue(see [curl/curl#1284 (comment)](https://github.com/curl/curl/issues/1284#issuecomment-282088514)), which makes curl wait a whole second before it sends its payload.  
>   
> Thank you for your efforts!  
  
You can easily add support for it. Here is an example: [Expect 100-continue (Server) 💡](https://www.boost.org/doc/libs/1_86_0/libs/beast/doc/html/beast/more_examples/expect_100_continue_server.html)
