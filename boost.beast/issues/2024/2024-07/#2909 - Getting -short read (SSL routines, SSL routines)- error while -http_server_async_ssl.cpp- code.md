# #2909 - Getting "short read (SSL routines, SSL routines)" error while "http_server_async_ssl.cpp" code [Closed]

> Username: shivank1111  
> Created at: 2024-07-29 09:12:09 UTC  
> Updated at: 2024-07-30 12:53:27 UTC  
> Closed at: 2024-07-30 12:53:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2909  

Version of Beast --1.83  
  
I am encountering a "short read (SSL routines, SSL routines)" error while running SSL integrated code files on my server. I was integrating Boost.Beast into a multithreaded chat application(making use of your chat-multi code sample) and faced this issue. So I tried with the basic example of http-ssl but the error persists.  
  
I have tested this with both my server certificates and the certificates provided by Boost.Beast, but the issue remains unresolved. I am in urgent need of a solution and would greatly appreciate your assistance.  
  
Attached are the code and certificates for your reference  
  
The server certificates I am using are the same as in the path #include "example/common/server_certificate.hpp".  
  
Command used to compile:  
g++ -std=c++11 http_server_async_ssl.cpp -o http  -lboost_system -lboost_filesystem -lpthread -lssl -lcrypto  
  
Thank you for your time and help.  
  
```#include "example/common/server_certificate.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>   
#include <boost/beast/ssl.hpp>  
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
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
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
    // ssl::error::stream_truncated, also known as an SSL "short read",  
    // indicates the peer closed the connection without performing the  
    // required closing handshake (for example, Google does this to  
    // improve performance). Generally this can be a security issue,  
    // but if your communication protocol is self-terminated (as  
    // it is with both HTTP and WebSocket) then you may simply  
    // ignore the lack of close_notify.  
    //  
    // https://github.com/boostorg/beast/issues/38  
    //  
    // https://security.stackexchange.com/questions/91435/how-to-handle-a-malicious-ssl-tls-shutdown  
    //  
    // When a short read would cut off the end of an HTTP message,  
    // Beast returns the error beast::http::error::partial_message.  
    // Therefore, if we see a short read here, it has occurred  
    // after the message has been completed, so it is safe to ignore it.  
  
    if(ec == net::ssl::error::stream_truncated)  
        return;  
  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Handles an HTTP server connection  
class session : public std::enable_shared_from_this<session>  
{  
    beast::ssl_stream<beast::tcp_stream> stream_;  
    beast::flat_buffer buffer_;  
    std::shared_ptr<std::string const> doc_root_;  
    http::request<http::string_body> req_;  
  
public:  
    // Take ownership of the socket  
    explicit  
    session(  
        tcp::socket&& socket,  
        ssl::context& ctx,  
        std::shared_ptr<std::string const> const& doc_root)  
        : stream_(std::move(socket), ctx)  
        , doc_root_(doc_root)  
    {  
    }  
  
    // Start the asynchronous operation  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch( stream_.get_executor(), beast::bind_front_handler( &session::on_run, shared_from_this()));  
    }  
  
    void  
    on_run()  
    {  
        // Set the timeout.  
        beast::get_lowest_layer(stream_).expires_after( std::chrono::seconds(30));  
  
        // Perform the SSL handshake  
        stream_.async_handshake(  
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
  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        // Make the request empty before reading,  
        // otherwise the operation behavior is undefined.  
        req_ = {};  
  
        // Set the timeout.  
        beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
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
        boost::ignore_unused(bytes_transferred);  
  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            return do_close();  
  
        if(ec)  
            return fail(ec, "read");  
  
        // Send the response  
        send_response(  
            handle_request(*doc_root_, std::move(req_)));  
    }  
  
    void  
    send_response(http::message_generator&& msg)  
    {  
        bool keep_alive = msg.keep_alive();  
  
        // Write the response  
        beast::async_write(  
            stream_,  
            std::move(msg),  
            beast::bind_front_handler(  
                &session::on_write,  
                this->shared_from_this(),  
                keep_alive));  
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
  
        // Read another request  
        do_read();  
    }  
  
    void  
    do_close()  
    {  
        // Set the timeout.  
        beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
        // Perform the SSL shutdown  
        stream_.async_shutdown(  
            beast::bind_front_handler(  
                &session::on_shutdown,  
                shared_from_this()));  
    }  
  
    void  
    on_shutdown(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "shutdown");  
  
        // At this point the connection is closed gracefully  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
    net::io_context& ioc_;  
    ssl::context& ctx_;  
    tcp::acceptor acceptor_;  
    std::shared_ptr<std::string const> doc_root_;  
  
public:  
    listener(  
        net::io_context& ioc,  
        ssl::context& ctx,  
        tcp::endpoint endpoint,  
        std::shared_ptr<std::string const> const& doc_root)  
        : ioc_(ioc)  
        , ctx_(ctx)  
        , acceptor_(ioc)  
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
            return; // To avoid infinite loop  
        }  
        else  
        {  
            // Create the session and run it  
            std::make_shared<session>(  
                std::move(socket),  
                ctx_,  
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
            "Usage: http-server-async-ssl <address> <port> <doc_root> <threads>\n" <<  
            "Example:\n" <<  
            "    http-server-async-ssl 0.0.0.0 8080 . 1\n";  
        return EXIT_FAILURE;  
    }  
    auto const address = net::ip::make_address(argv[1]);  
    auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto const doc_root = std::make_shared<std::string>(argv[3]);  
    auto const threads = std::max<int>(1, std::atoi(argv[4]));  
  
    // The io_context is required for all I/O  
    net::io_context ioc{threads};  
  
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::tlsv12};  
  
    // This holds the self-signed certificate used by the server  
    load_server_certificate(ctx);  
  
    // Create and launch a listening port  
    std::make_shared<listener>(  
        ioc,  
        ctx,  
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
}```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-07-29 09:34:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2909#issuecomment-2255454060  

At which step are you getting the short read error?  
I recommend reading this section of the documentation: https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/ssl_tls_shutdown.html

---

## Comment 2

> Username: shivank1111  
> Created at: 2024-07-29 09:53:28 UTC  
> Updated at: 2024-07-29 10:07:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2909#issuecomment-2255494694  

Hi @ashtum, I am going through the above shared link till then kindly help me with the below case.   
I have added cout in every function, after which if I am trying to connect through postman then I am getting the error.  
I am using the same certificates as used with basic examples of boost beast library.  
Flow after which I am getting the error  
--------------------------------------------  
After starting the binary  
--------------------------  
LISTENER CONSTRUCTOR  
LISTENER RUN  
LISTENER DO_ACCEPT  
  
  
Trying to connect the server  
----------------------------  
LISTENER ON_ACCEPT  
HTTP CONSTRUCTOR  
HTTP RUN  
HTTP ON_RUN  
LISTENER DO_ACCEPT  
HTTP ON_HANDSHAKE  
HTTP DO_READ  
HTTP ON_READ  
FAIL  
Error in http_session:: fail: ec.message: short read (SSL routines, SSL routines)  
  
Kindly help me with the same. Where and how I should make changes such that this issue can be resolved.  
  
New http_server_async_ssl.cpp  
----------------------------------  
```  
#include "example/common/server_certificate.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>   
#include <boost/beast/ssl.hpp>  
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
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
// Return a reasonable mime type based on the extension of a file.  
beast::string_view  
mime_type(beast::string_view path)  
{  
    std::cout<<"MIME TYPES"<<"\n";  
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
    std::cout<<"PATH CAT"<<"\n";  
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
    std::cout<<"HANDLE REQUEST"<<"\n";  
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
    // ssl::error::stream_truncated, also known as an SSL "short read",  
    // indicates the peer closed the connection without performing the  
    // required closing handshake (for example, Google does this to  
    // improve performance). Generally this can be a security issue,  
    // but if your communication protocol is self-terminated (as  
    // it is with both HTTP and WebSocket) then you may simply  
    // ignore the lack of close_notify.  
    //  
    // https://github.com/boostorg/beast/issues/38  
    //  
    // https://security.stackexchange.com/questions/91435/how-to-handle-a-malicious-ssl-tls-shutdown  
    //  
    // When a short read would cut off the end of an HTTP message,  
    // Beast returns the error beast::http::error::partial_message.  
    // Therefore, if we see a short read here, it has occurred  
    // after the message has been completed, so it is safe to ignore it.  
	std::cout<<"FAIL"<<"\n";  
	std::cout<<"Error in http_session:: fail: ec.message: "<< ec.message().c_str()<<"\n";  
    if(ec == net::ssl::error::stream_truncated)  
        return;  
  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Handles an HTTP server connection  
class session : public std::enable_shared_from_this<session>  
{  
	beast::ssl_stream<beast::tcp_stream> stream_;  
    beast::flat_buffer buffer_;  
    std::shared_ptr<std::string const> doc_root_;  
    http::request<http::string_body> req_;  
  
public:  
    // Take ownership of the socket  
    explicit  
    session(  
        tcp::socket&& socket,  
        ssl::context& ctx,  
        std::shared_ptr<std::string const> const& doc_root)  
        : stream_(std::move(socket), ctx)  
        , doc_root_(doc_root)  
    {  
		std::cout<<"HTTP CONSTRUCTOR"<<"\n";  
    }  
  
    // Start the asynchronous operation  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
		std::cout<<"HTTP RUN"<<"\n";  
        net::dispatch( stream_.get_executor(), beast::bind_front_handler( &session::on_run, shared_from_this()));  
    }  
  
    void  
    on_run()  
    {  
        std::cout<<"HTTP ON_RUN"<<"\n";  
		// Set the timeout.  
        beast::get_lowest_layer(stream_).expires_after( std::chrono::seconds(30));  
  
        // Perform the SSL handshake  
        stream_.async_handshake(  
            ssl::stream_base::server,  
            beast::bind_front_handler(  
                &session::on_handshake,  
                shared_from_this()));  
    }  
  
    void  
    on_handshake(beast::error_code ec)  
    {  
        std::cout<<"HTTP ON_HANDSHAKE"<<"\n";  
		if(ec)  
            return fail(ec, "handshake");  
  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        // Make the request empty before reading,  
        // otherwise the operation behavior is undefined.  
        std::cout<<"HTTP DO_READ"<<"\n";  
		req_ = {};  
  
        // Set the timeout.  
        beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
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
        std::cout<<"HTTP ON_READ"<<"\n";  
		boost::ignore_unused(bytes_transferred);  
  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            return do_close();  
  
        if(ec)  
            return fail(ec, "read");  
  
        // Send the response  
        send_response(  
            handle_request(*doc_root_, std::move(req_)));  
    }  
  
    void  
    send_response(http::message_generator&& msg)  
    {  
        std::cout<<"HTTP SEND_RESPONSE"<<"\n";  
		bool keep_alive = msg.keep_alive();  
  
        // Write the response  
        beast::async_write(  
            stream_,  
            std::move(msg),  
            beast::bind_front_handler(  
                &session::on_write,  
                this->shared_from_this(),  
                keep_alive));  
    }  
  
    void  
    on_write(  
        bool keep_alive,  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        std::cout<<"HTTP ON_WRITE"<<"\n";  
		boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        if(! keep_alive)  
        {  
            // This means we should close the connection, usually because  
            // the response indicated the "Connection: close" semantic.  
            return do_close();  
        }  
  
        // Read another request  
        do_read();  
    }  
  
    void  
    do_close()  
    {  
        std::cout<<"HTTP DO_CLOSE"<<"\n";  
		// Set the timeout.  
        beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
        // Perform the SSL shutdown  
        stream_.async_shutdown(  
            beast::bind_front_handler(  
                &session::on_shutdown,  
                shared_from_this()));  
    }  
  
    void  
    on_shutdown(beast::error_code ec)  
    {  
        std::cout<<"HTTP ON_SHUTDOWN"<<"\n";  
		if(ec)  
            return fail(ec, "shutdown");  
  
        // At this point the connection is closed gracefully  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
    net::io_context& ioc_;  
    ssl::context& ctx_;  
    tcp::acceptor acceptor_;  
    std::shared_ptr<std::string const> doc_root_;  
  
public:  
    listener(  
        net::io_context& ioc,  
        ssl::context& ctx,  
        tcp::endpoint endpoint,  
        std::shared_ptr<std::string const> const& doc_root)  
        : ioc_(ioc)  
        , ctx_(ctx)  
        , acceptor_(ioc)  
        , doc_root_(doc_root)  
    {  
        std::cout<<"LISTENER CONSTRUCTOR"<<"\n";  
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
std::cout<<"LISTENER RUN"<<"\n";         
	   do_accept();  
    }  
  
private:  
    void  
    do_accept()  
    {  
        std::cout<<"LISTENER DO_ACCEPT"<<"\n";  
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
        std::cout<<"LISTENER ON_ACCEPT"<<"\n";  
		if(ec)  
        {  
            fail(ec, "accept");  
            return; // To avoid infinite loop  
        }  
        else  
        {  
            // Create the session and run it  
            std::make_shared<session>(  
                std::move(socket),  
                ctx_,  
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
            "Usage: http-server-async-ssl <address> <port> <doc_root> <threads>\n" <<  
            "Example:\n" <<  
            "    http-server-async-ssl 0.0.0.0 8080 . 1\n";  
        return EXIT_FAILURE;  
    }  
    auto const address = net::ip::make_address(argv[1]);  
    auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto const doc_root = std::make_shared<std::string>(argv[3]);  
    auto const threads = std::max<int>(1, std::atoi(argv[4]));  
  
    // The io_context is required for all I/O  
    net::io_context ioc{threads};  
  
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::tlsv12};  
  
    // This holds the self-signed certificate used by the server  
    load_server_certificate(ctx);  
  
    // Create and launch a listening port  
    std::make_shared<listener>(  
        ioc,  
        ctx,  
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

---

## Comment 3

> Username: ashtum  
> Created at: 2024-07-29 10:18:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2909#issuecomment-2255546532  

The certificate used in the server examples is self-signed. You need to either turn off SSL verification in Postman or add the certificates to the trusted list.  
https://learning.postman.com/docs/sending-requests/authorization/certificates/#troubleshooting-certificate-errors  
https://learning.postman.com/docs/sending-requests/authorization/certificates/

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-07-29 13:53:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2909#issuecomment-2256012826  

@ashtum we need a section in the docs "Using Certificates" with a sub-section "Troubleshooting" etc. Please open an issue

---

## Comment 5

> Username: shivank1111  
> Created at: 2024-07-30 12:53:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2909#issuecomment-2258280878  

Thankyou @ashtum. This part is working fine. So I am closing the issue.
