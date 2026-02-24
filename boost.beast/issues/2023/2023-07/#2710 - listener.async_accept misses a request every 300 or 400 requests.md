# #2710 - listener.async_accept misses  a request every 300 or 400 requests [Closed]

> Username: gszaboky  
> Created at: 2023-07-28 19:04:41 UTC  
> Updated at: 2023-08-10 16:58:49 UTC  
> Closed at: 2023-08-10 16:58:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2710  

I have a server based on boost/beast. I have modeled server after the example http_server_async_ssl.cpp. I have this code for the listener:  
  
`    void  
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
`   
  
I have a simple script that sends 2 requests to the server, one after the other.  This is repeated 1000 times. After several 100 requests, I  see a request is missed. If I send another request, it works and the server continues. I have instrumented the code to generate a trace. Here is the trace:  
  
 starting the listener : do_accept()|accept_cnt: 1  
first request comes in: on_accept()|tls_cnt: 1  
                      : do_accept()|accept_cnt: 2  
                      : session::do_read()  
                      : session::on_read()  
                      : request proc started   
                      : request proc finished.  
                      : session::on_write()  
                      : session::on_write() connection closed  
  
                      : on_accept()|tls_cnt: 2  
                      : do_accept()|accept_cnt: 3  
                      : session::do_read()  
                      : session::on_read()  
                      : request proc started  
                      : request proc finished  
                      : session::on_write()  
                      : session::on_write() connection closed  
  
...  
  
                      : on_accept()|tls_cnt: 537  
                      : do_accept()|accept_cnt: 538  
                      : session::do_read()  
                      : session::on_read()  
                      : request proc started   
                      : request proc finished  
                      : session::on_write()  
                      : session::on_write() connection closed  
  
At this point the client sends request #538 and the server does not respond. If I send another request, the server responds. I am at wits end trying to figure out why request 538 was missed. Any ideas? How do debug this. There must be some state that I can look at, but have no clue where to start.  
  
Regards,  
George

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-07-28 19:07:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2710#issuecomment-1656197259  

Well it could depend on your operating system, and the socket backlog:  
  
https://www.linuxjournal.com/files/linuxjournal.com/linuxjournal/articles/023/2333/2333s2.html  
  
if you are running Windows Home Edition, the operating system limits the rate at which you can accept outside connections.

---

## Comment 2

> Username: gszaboky  
> Created at: 2023-07-28 19:36:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2710#issuecomment-1656226720  

Thanks for the quick response. I am running Linux. A little more information: the boot/beast server is a replacement for a server that was written in python and the above-mentioned script ran against the python server with no issues. Thanks for the link. I am going to look at the backlog parameter.

---

## Comment 3

> Username: gszaboky  
> Created at: 2023-07-28 19:44:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2710#issuecomment-1656233564  

The backlog parameter is set really high, like 4K.  
  
`  acceptor_.listen(boost::asio::socket_base::max_listen_connections, ec);  
  if (ec)   
  {  
    error_callback(ec);  
    WS_ERR << "listen " << ec.message();  
    return;  
  }`

---

## Comment 4

> Username: ashtum  
> Created at: 2023-07-28 20:25:45 UTC  
> Updated at: 2023-07-28 20:27:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2710#issuecomment-1656290814  

Could you provide a minimal reproducible example code, so that we can test in our machine.  
Have you tried to run the code on a single threaded executor?

---

## Comment 5

> Username: gszaboky  
> Created at: 2023-08-09 21:36:22 UTC  
> Updated at: 2023-08-09 21:54:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2710#issuecomment-1672185346  

Pulling the existing application apart provide to be a big job. After playing around with some existing examples see the issue using the following advanced flex server.   
  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/buffer.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/asio/signal_set.hpp>  
#include <boost/asio/ssl/context.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/make_unique.hpp>  
#include <boost/optional.hpp>  
#include <algorithm>  
#include <cstddef>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
namespace beast = boost::beast;                 // from <boost/beast.hpp>  
namespace http = beast::http;                   // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket;         // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;                    // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;               // from <boost/asio/ssl.hpp>  
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
  
//------------------------------------------------------------------------------  
  
// Echoes back all received WebSocket messages.  
// This uses the Curiously Recurring Template Pattern so that  
// the same code works with both SSL streams and regular sockets.  
template<class Derived>  
class websocket_session  
{  
    // Access the derived class, this is part of  
    // the Curiously Recurring Template Pattern idiom.  
    Derived&  
    derived()  
    {  
        return static_cast<Derived&>(*this);  
    }  
  
    beast::flat_buffer buffer_;  
  
    // Start the asynchronous operation  
    template<class Body, class Allocator>  
    void  
    do_accept(http::request<Body, http::basic_fields<Allocator>> req)  
    {  
        // Set suggested timeout settings for the websocket  
        derived().ws().set_option(  
            websocket::stream_base::timeout::suggested(  
                beast::role_type::server));  
  
        // Set a decorator to change the Server of the handshake  
        derived().ws().set_option(  
            websocket::stream_base::decorator(  
            [](websocket::response_type& res)  
            {  
                res.set(http::field::server,  
                    std::string(BOOST_BEAST_VERSION_STRING) +  
                        " advanced-server-flex");  
            }));  
  
        // Accept the websocket handshake  
        derived().ws().async_accept(  
            req,  
            beast::bind_front_handler(  
                &websocket_session::on_accept,  
                derived().shared_from_this()));  
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
        derived().ws().async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &websocket_session::on_read,  
                derived().shared_from_this()));  
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
            return fail(ec, "read");  
  
        // Echo the message  
        derived().ws().text(derived().ws().got_text());  
        derived().ws().async_write(  
            buffer_.data(),  
            beast::bind_front_handler(  
                &websocket_session::on_write,  
                derived().shared_from_this()));  
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
  
public:  
    // Start the asynchronous operation  
    template<class Body, class Allocator>  
    void  
    run(http::request<Body, http::basic_fields<Allocator>> req)  
    {  
        // Accept the WebSocket upgrade request  
        do_accept(std::move(req));  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Handles a plain WebSocket connection  
class plain_websocket_session  
    : public websocket_session<plain_websocket_session>  
    , public std::enable_shared_from_this<plain_websocket_session>  
{  
    websocket::stream<beast::tcp_stream> ws_;  
  
public:  
    // Create the session  
    explicit  
    plain_websocket_session(  
        beast::tcp_stream&& stream)  
        : ws_(std::move(stream))  
    {  
    }  
  
    // Called by the base class  
    websocket::stream<beast::tcp_stream>&  
    ws()  
    {  
        return ws_;  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Handles an SSL WebSocket connection  
class ssl_websocket_session  
    : public websocket_session<ssl_websocket_session>  
    , public std::enable_shared_from_this<ssl_websocket_session>  
{  
    websocket::stream<  
        beast::ssl_stream<beast::tcp_stream>> ws_;  
  
public:  
    // Create the ssl_websocket_session  
    explicit  
    ssl_websocket_session(  
        beast::ssl_stream<beast::tcp_stream>&& stream)  
        : ws_(std::move(stream))  
    {  
    }  
  
    // Called by the base class  
    websocket::stream<  
        beast::ssl_stream<beast::tcp_stream>>&  
    ws()  
    {  
        return ws_;  
    }  
};  
  
//------------------------------------------------------------------------------  
  
template<class Body, class Allocator>  
void  
make_websocket_session(  
    beast::tcp_stream stream,  
    http::request<Body, http::basic_fields<Allocator>> req)  
{  
    std::make_shared<plain_websocket_session>(  
        std::move(stream))->run(std::move(req));  
}  
  
template<class Body, class Allocator>  
void  
make_websocket_session(  
    beast::ssl_stream<beast::tcp_stream> stream,  
    http::request<Body, http::basic_fields<Allocator>> req)  
{  
    std::make_shared<ssl_websocket_session>(  
        std::move(stream))->run(std::move(req));  
}  
  
//------------------------------------------------------------------------------  
  
// Handles an HTTP server connection.  
// This uses the Curiously Recurring Template Pattern so that  
// the same code works with both SSL streams and regular sockets.  
template<class Derived>  
class http_session  
{  
    // Access the derived class, this is part of  
    // the Curiously Recurring Template Pattern idiom.  
    Derived&  
    derived()  
    {  
        return static_cast<Derived&>(*this);  
    }  
  
    // This queue is used for HTTP pipelining.  
    class queue  
    {  
        enum  
        {  
            // Maximum number of responses we will queue  
            limit = 8  
        };  
  
        // The type-erased, saved work item  
        struct work  
        {  
            virtual ~work() = default;  
            virtual void operator()() = 0;  
        };  
  
        http_session& self_;  
        std::vector<std::unique_ptr<work>> items_;  
  
    public:  
        explicit  
        queue(http_session& self)  
            : self_(self)  
        {  
            static_assert(limit > 0, "queue limit must be positive");  
            items_.reserve(limit);  
        }  
  
        // Returns `true` if we have reached the queue limit  
        bool  
        is_full() const  
        {  
            return items_.size() >= limit;  
        }  
  
        // Called when a message finishes sending  
        // Returns `true` if the caller should initiate a read  
        bool  
        on_write()  
        {  
            BOOST_ASSERT(! items_.empty());  
            auto const was_full = is_full();  
            items_.erase(items_.begin());  
            if(! items_.empty())  
                (*items_.front())();  
            return was_full;  
        }  
  
        // Called by the HTTP handler to send a response.  
        template<bool isRequest, class Body, class Fields>  
        void  
        operator()(http::message<isRequest, Body, Fields>&& msg)  
        {  
            // This holds a work item  
            struct work_impl : work  
            {  
                http_session& self_;  
                http::message<isRequest, Body, Fields> msg_;  
  
                work_impl(  
                    http_session& self,  
                    http::message<isRequest, Body, Fields>&& msg)  
                    : self_(self)  
                    , msg_(std::move(msg))  
                {  
                }  
  
                void  
                operator()()  
                {  
                    http::async_write(  
                        self_.derived().stream(),  
                        msg_,  
                        beast::bind_front_handler(  
                            &http_session::on_write,  
                            self_.derived().shared_from_this(),  
                            msg_.need_eof()));  
                }  
            };  
  
            // Allocate and store the work  
            items_.push_back(  
                boost::make_unique<work_impl>(self_, std::move(msg)));  
  
            // If there was no previous work, start this one  
            if(items_.size() == 1)  
                (*items_.front())();  
        }  
    };  
  
    std::shared_ptr<std::string const> doc_root_;  
    queue queue_;  
  
    // The parser is stored in an optional container so we can  
    // construct it from scratch it at the beginning of each new message.  
    boost::optional<http::request_parser<http::string_body>> parser_;  
  
protected:  
    beast::flat_buffer buffer_;  
  
public:  
    // Construct the session  
    http_session(  
        beast::flat_buffer buffer,  
        std::shared_ptr<std::string const> const& doc_root)  
        : doc_root_(doc_root)  
        , queue_(*this)  
        , buffer_(std::move(buffer))  
    {  
    }  
  
    void  
    do_read()  
    {  
        // Construct a new parser for each message  
        parser_.emplace();  
  
        // Apply a reasonable limit to the allowed size  
        // of the body in bytes to prevent abuse.  
        parser_->body_limit(10000);  
  
        // Set the timeout.  
        beast::get_lowest_layer(  
            derived().stream()).expires_after(std::chrono::seconds(30));  
  
        // Read a request using the parser-oriented interface  
        http::async_read(  
            derived().stream(),  
            buffer_,  
            *parser_,  
            beast::bind_front_handler(  
                &http_session::on_read,  
                derived().shared_from_this()));  
    }  
  
    void  
    on_read(beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            return derived().do_eof();  
  
        if(ec)  
            return fail(ec, "read");  
  
        // See if it is a WebSocket Upgrade  
        if(websocket::is_upgrade(parser_->get()))  
        {  
            // Disable the timeout.  
            // The websocket::stream uses its own timeout settings.  
            beast::get_lowest_layer(derived().stream()).expires_never();  
  
            // Create a websocket session, transferring ownership  
            // of both the socket and the HTTP request.  
            return make_websocket_session(  
                derived().release_stream(),  
                parser_->release());  
        }  
  
        // Send the response  
        handle_request(*doc_root_, parser_->release(), queue_);  
  
        // If we aren't at the queue limit, try to pipeline another request  
        if(! queue_.is_full())  
            do_read();  
    }  
  
    void  
    on_write(bool close, beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        if(close)  
        {  
            // This means we should close the connection, usually because  
            // the response indicated the "Connection: close" semantic.  
            return derived().do_eof();  
        }  
  
        // Inform the queue that a write completed  
        if(queue_.on_write())  
        {  
            // Read another request  
            do_read();  
        }  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Handles a plain HTTP connection  
class plain_http_session  
    : public http_session<plain_http_session>  
    , public std::enable_shared_from_this<plain_http_session>  
{  
    beast::tcp_stream stream_;  
  
public:  
    // Create the session  
    plain_http_session(  
        beast::tcp_stream&& stream,  
        beast::flat_buffer&& buffer,  
        std::shared_ptr<std::string const> const& doc_root)  
        : http_session<plain_http_session>(std::move(buffer),doc_root)  
        , stream_(std::move(stream))  
    {  
    }  
  
    // Start the session  
    void  
    run()  
    {  
        this->do_read();  
    }  
  
    // Called by the base class  
    beast::tcp_stream&  
    stream()  
    {  
        return stream_;  
    }  
  
    // Called by the base class  
    beast::tcp_stream  
    release_stream()  
    {  
        return std::move(stream_);  
    }  
  
    // Called by the base class  
    void  
    do_eof()  
    {  
        // Send a TCP shutdown  
        beast::error_code ec;  
        stream_.socket().shutdown(tcp::socket::shutdown_send, ec);  
  
        // At this point the connection is closed gracefully  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Handles an SSL HTTP connection  
class ssl_http_session  
    : public http_session<ssl_http_session>  
    , public std::enable_shared_from_this<ssl_http_session>  
{  
    beast::ssl_stream<beast::tcp_stream> stream_;  
  
public:  
    // Create the http_session  
    ssl_http_session(  
        beast::tcp_stream&& stream,  
        ssl::context& ctx,  
        beast::flat_buffer&& buffer,  
        std::shared_ptr<std::string const> const& doc_root)  
        : http_session<ssl_http_session>(  
            std::move(buffer),  
            doc_root)  
        , stream_(std::move(stream), ctx)  
    {  
    }  
  
    // Start the session  
    void  
    run()  
    {  
        // Set the timeout.  
        beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
        // Perform the SSL handshake  
        // Note, this is the buffered version of the handshake.  
        stream_.async_handshake(  
            ssl::stream_base::server,  
            buffer_.data(),  
            beast::bind_front_handler(  
                &ssl_http_session::on_handshake,  
                shared_from_this()));  
    }  
  
    // Called by the base class  
    beast::ssl_stream<beast::tcp_stream>&  
    stream()  
    {  
        return stream_;  
    }  
  
    // Called by the base class  
    beast::ssl_stream<beast::tcp_stream>  
    release_stream()  
    {  
        return std::move(stream_);  
    }  
  
    // Called by the base class  
    void  
    do_eof()  
    {  
        // Set the timeout.  
        beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
        // Perform the SSL shutdown  
        stream_.async_shutdown(  
            beast::bind_front_handler(  
                &ssl_http_session::on_shutdown,  
                shared_from_this()));  
    }  
  
private:  
    void  
    on_handshake(  
        beast::error_code ec,  
        std::size_t bytes_used)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
  
        // Consume the portion of the buffer used by the handshake  
        buffer_.consume(bytes_used);  
  
        do_read();  
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
  
// Detects SSL handshakes  
class detect_session : public std::enable_shared_from_this<detect_session>  
{  
    beast::tcp_stream stream_;  
    ssl::context& ctx_;  
    std::shared_ptr<std::string const> doc_root_;  
    beast::flat_buffer buffer_;  
  
public:  
    explicit  
    detect_session(  
        tcp::socket&& socket,  
        ssl::context& ctx,  
        std::shared_ptr<std::string const> const& doc_root)  
        : stream_(std::move(socket))  
        , ctx_(ctx)  
        , doc_root_(doc_root)  
    {  
    }  
  
    // Launch the detector  
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
                &detect_session::on_run,  
                this->shared_from_this()));  
    }  
  
    void  
    on_run()  
    {  
        // Set the timeout.  
        stream_.expires_after(std::chrono::seconds(30));  
  
        beast::async_detect_ssl(  
            stream_,  
            buffer_,  
            beast::bind_front_handler(  
                &detect_session::on_detect,  
                this->shared_from_this()));  
    }  
  
    void  
    on_detect(beast::error_code ec, bool result)  
    {  
        if(ec)  
            return fail(ec, "detect");  
  
        if(result)  
        {  
            // Launch SSL session  
            std::make_shared<ssl_http_session>(  
                std::move(stream_),  
                ctx_,  
                std::move(buffer_),  
                doc_root_)->run();  
            return;  
        }  
  
        // Launch plain session  
        std::make_shared<plain_http_session>(  
            std::move(stream_),  
            std::move(buffer_),  
            doc_root_)->run();  
    }  
};  
  
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
            // Create the detector http_session and run it  
            std::make_shared<detect_session>(  
                std::move(socket),  
                ctx_,  
                doc_root_)->run();  
        }  
  
        // Accept another connection  
        do_accept();  
    }  
};  
  
  
//------------------------------------------------------------------------------  
  
/*  Load a signed certificate into the ssl context, and configure  
    the context for use with a server.  
  
    For this to work with the browser or operating system, it is  
    necessary to import the "Beast Test CA" certificate into  
    the local certificate store, browser, or operating system  
    depending on your environment Please see the documentation  
    accompanying the Beast certificate for more details.  
*/  
inline  
void  
load_server_certificate(boost::asio::ssl::context& ctx)  
{  
    /*  
        The certificate was generated from bash on Ubuntu (OpenSSL 1.1.1f) using:  
  
        openssl dhparam -out dh.pem 2048  
        openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 10000 -out cert.pem -subj "/C=US/ST=CA/L=Los Angeles/O=Beast/CN=www.example.com"  
    */  
  
    std::string const cert =  
        "-----BEGIN CERTIFICATE-----\n"  
        "MIIDlTCCAn2gAwIBAgIUOLxr3q7Wd/pto1+2MsW4fdRheCIwDQYJKoZIhvcNAQEL\n"  
        "BQAwWjELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAkNBMRQwEgYDVQQHDAtMb3MgQW5n\n"  
        "ZWxlczEOMAwGA1UECgwFQmVhc3QxGDAWBgNVBAMMD3d3dy5leGFtcGxlLmNvbTAe\n"  
        "Fw0yMTA3MDYwMTQ5MjVaFw00ODExMjEwMTQ5MjVaMFoxCzAJBgNVBAYTAlVTMQsw\n"  
        "CQYDVQQIDAJDQTEUMBIGA1UEBwwLTG9zIEFuZ2VsZXMxDjAMBgNVBAoMBUJlYXN0\n"  
        "MRgwFgYDVQQDDA93d3cuZXhhbXBsZS5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IB\n"  
        "DwAwggEKAoIBAQCz0GwgnxSBhygxBdhTHGx5LDLIJSuIDJ6nMwZFvAjdhLnB/vOT\n"  
        "Lppr5MKxqQHEpYdyDYGD1noBoz4TiIRj5JapChMgx58NLq5QyXkHV/ONT7yi8x05\n"  
        "P41c2F9pBEnUwUxIUG1Cb6AN0cZWF/wSMOZ0w3DoBhnl1sdQfQiS25MTK6x4tATm\n"  
        "Wm9SJc2lsjWptbyIN6hFXLYPXTwnYzCLvv1EK6Ft7tMPc/FcJpd/wYHgl8shDmY7\n"  
        "rV+AiGTxUU35V0AzpJlmvct5aJV/5vSRRLwT9qLZSddE9zy/0rovC5GML6S7BUC4\n"  
        "lIzJ8yxzOzSStBPxvdrOobSSNlRZIlE7gnyNAgMBAAGjUzBRMB0GA1UdDgQWBBR+\n"  
        "dYtY9zmFSw9GYpEXC1iJKHC0/jAfBgNVHSMEGDAWgBR+dYtY9zmFSw9GYpEXC1iJ\n"  
        "KHC0/jAPBgNVHRMBAf8EBTADAQH/MA0GCSqGSIb3DQEBCwUAA4IBAQBzKrsiYywl\n"  
        "RKeB2LbddgSf7ahiQMXCZpAjZeJikIoEmx+AmjQk1bam+M7WfpRAMnCKooU+Utp5\n"  
        "TwtijjnJydkZHFR6UH6oCWm8RsUVxruao/B0UFRlD8q+ZxGd4fGTdLg/ztmA+9oC\n"  
        "EmrcQNdz/KIxJj/fRB3j9GM4lkdaIju47V998Z619E/6pt7GWcAySm1faPB0X4fL\n"  
        "FJ6iYR2r/kJLoppPqL0EE49uwyYQ1dKhXS2hk+IIfA9mBn8eAFb/0435A2fXutds\n"  
        "qhvwIOmAObCzcoKkz3sChbk4ToUTqbC0TmFAXI5Upz1wnADzjpbJrpegCA3pmvhT\n"  
        "7356drqnCGY9\n"  
        "-----END CERTIFICATE-----\n";  
  
    std::string const key =  
        "-----BEGIN PRIVATE KEY-----\n"  
        "MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQCz0GwgnxSBhygx\n"  
        "BdhTHGx5LDLIJSuIDJ6nMwZFvAjdhLnB/vOTLppr5MKxqQHEpYdyDYGD1noBoz4T\n"  
        "iIRj5JapChMgx58NLq5QyXkHV/ONT7yi8x05P41c2F9pBEnUwUxIUG1Cb6AN0cZW\n"  
        "F/wSMOZ0w3DoBhnl1sdQfQiS25MTK6x4tATmWm9SJc2lsjWptbyIN6hFXLYPXTwn\n"  
        "YzCLvv1EK6Ft7tMPc/FcJpd/wYHgl8shDmY7rV+AiGTxUU35V0AzpJlmvct5aJV/\n"  
        "5vSRRLwT9qLZSddE9zy/0rovC5GML6S7BUC4lIzJ8yxzOzSStBPxvdrOobSSNlRZ\n"  
        "IlE7gnyNAgMBAAECggEAY0RorQmldGx9D7M+XYOPjsWLs1px0cXFwGA20kCgVEp1\n"  
        "kleBeHt93JqJsTKwOzN2tswl9/ZrnIPWPUpcbBlB40ggjzQk5k4jBY50Nk2jsxuV\n"  
        "9A9qzrP7AoqhAYTQjZe42SMtbkPZhEeOyvCqxBAi6csLhcv4eB4+In0kQo7dfvLs\n"  
        "Xu/3WhSsuAWqdD9EGnhD3n+hVTtgiasRe9318/3R9DzP+IokoQGOtXm+1dsfP0mV\n"  
        "8XGzQHBpUtJNn0yi6SC4kGEQuKkX33zORlSnZgT5VBLofNgra0THd7x3atOx1lbr\n"  
        "V0QizvCdBa6j6FwhOQwW8UwgOCnUbWXl/Xn4OaofMQKBgQDdRXSMyys7qUMe4SYM\n"  
        "Mdawj+rjv0Hg98/xORuXKEISh2snJGKEwV7L0vCn468n+sM19z62Axz+lvOUH8Qr\n"  
        "hLkBNqJvtIP+b0ljRjem78K4a4qIqUlpejpRLw6a/+44L76pMJXrYg3zdBfwzfwu\n"  
        "b9NXdwHzWoNuj4v36teGP6xOUwKBgQDQCT52XX96NseNC6HeK5BgWYYjjxmhksHi\n"  
        "stjzPJKySWXZqJpHfXI8qpOd0Sd1FHB+q1s3hand9c+Rxs762OXlqA9Q4i+4qEYZ\n"  
        "qhyRkTsl+2BhgzxmoqGd5gsVT7KV8XqtuHWLmetNEi+7+mGSFf2iNFnonKlvT1JX\n"  
        "4OQZC7ntnwKBgH/ORFmmaFxXkfteFLnqd5UYK5ZMvGKTALrWP4d5q2BEc7HyJC2F\n"  
        "+5lDR9nRezRedS7QlppPBgpPanXeO1LfoHSA+CYJYEwwP3Vl83Mq/Y/EHgp9rXeN\n"  
        "L+4AfjEtLo2pljjnZVDGHETIg6OFdunjkXDtvmSvnUbZBwG11bMnSAEdAoGBAKFw\n"  
        "qwJb6FNFM3JnNoQctnuuvYPWxwM1yjRMqkOIHCczAlD4oFEeLoqZrNhpuP8Ij4wd\n"  
        "GjpqBbpzyVLNP043B6FC3C/edz4Lh+resjDczVPaUZ8aosLbLiREoxE0udfWf2dU\n"  
        "oBNnrMwwcs6jrRga7Kr1iVgUSwBQRAxiP2CYUv7tAoGBAKdPdekPNP/rCnHkKIkj\n"  
        "o13pr+LJ8t+15vVzZNHwPHUWiYXFhG8Ivx7rqLQSPGcuPhNss3bg1RJiZAUvF6fd\n"  
        "e6QS4EZM9dhhlO2FmPQCJMrRVDXaV+9TcJZXCbclQnzzBus9pwZZyw4Anxo0vmir\n"  
        "nOMOU6XI4lO9Xge/QDEN4Y2R\n"  
        "-----END PRIVATE KEY-----\n";  
  
    std::string const dh =  
        "-----BEGIN DH PARAMETERS-----\n"  
        "MIIBCAKCAQEArzQc5mpm0Fs8yahDeySj31JZlwEphUdZ9StM2D8+Fo7TMduGtSi+\n"  
        "/HRWVwHcTFAgrxVdm+dl474mOUqqaz4MpzIb6+6OVfWHbQJmXPepZKyu4LgUPvY/\n"  
        "4q3/iDMjIS0fLOu/bLuObwU5ccZmDgfhmz1GanRlTQOiYRty3FiOATWZBRh6uv4u\n"  
        "tff4A9Bm3V9tLx9S6djq31w31Gl7OQhryodW28kc16t9TvO1BzcV3HjRPwpe701X\n"  
        "oEEZdnZWANkkpR/m/pfgdmGPU66S2sXMHgsliViQWpDCYeehrvFRHEdR9NV+XJfC\n"  
        "QMUk26jPTIVTLfXmmwU0u8vUkpR7LQKkwwIBAg==\n"  
        "-----END DH PARAMETERS-----\n";  
      
    ctx.set_password_callback(  
        [](std::size_t,  
            boost::asio::ssl::context_base::password_purpose)  
        {  
            return "test";  
        });  
  
    ctx.set_options(  
        boost::asio::ssl::context::default_workarounds |  
        boost::asio::ssl::context::no_sslv2 |  
        boost::asio::ssl::context::single_dh_use);  
  
    ctx.use_certificate_chain(  
        boost::asio::buffer(cert.data(), cert.size()));  
  
    ctx.use_private_key(  
        boost::asio::buffer(key.data(), key.size()),  
        boost::asio::ssl::context::file_format::pem);  
  
    ctx.use_tmp_dh(  
        boost::asio::buffer(dh.data(), dh.size()));  
}  
  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[])  
{  
    // Check command line arguments.  
    if (argc != 5)  
    {  
        std::cerr <<  
            "Usage: advanced-server-flex <address> <port> <doc_root> <threads>\n" <<  
            "Example:\n" <<  
            "    advanced-server-flex 0.0.0.0 8080 . 1\n";  
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
  
The server is run as root, on port 80 with the following command  
  
advanced-server-flex 0.0.0.0 80. 1  
  
or   
  
advanced-server-flex 0.0.0.0 443 . 1  
  
I have a simple index.html file with the following contents:  
`{"index": "me"}`  
  
I have run curl clients, nodejs clients and python clients. The following is a curl script for port 80  
  
```  
#!/bin/bash  
#set -x  
  
  
target=$IPADDR  
port=80  
  
for ((i=1; i<=2000; i++)) {  
     echo -e "\n test: $i"  
     curl -v \  
          http://$target:$port/  
}  
  
  
```  
A side note, if I am running TLS on port 443 with a nodejs client, I see "shutdown: Operation canceled" on the server for every request/response, even though it was successful. At least from a client perspective. Not sure why. I do not see this with curl.   
  
Any comments or ideas would be greatly appreciated.   
  
Thank You.

---

## Comment 6

> Username: ashtum  
> Created at: 2023-08-10 05:30:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2710#issuecomment-1672579318  

I have built and tested your code on Ubuntu 22.04 using gcc 12 and boost 1.82.0. There were no issues, and I even increased the number of iterations in the tests to 100,000. (I tested it in both single-threaded and multi-threaded modes.)  
It seems like there might be an issue with your operating system. Have you considered using the latest version of boost or changing your compiler?  
I would suggest trying to narrow down the problem even further, maybe by using a simple HTTP server that returns a string.

---

## Comment 7

> Username: gszaboky  
> Created at: 2023-08-10 16:58:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2710#issuecomment-1673583335  

Thanks. I have reproduced your results with Ubuntu 20.04, and boost 1.80. This is more aligned with the target I am working on. The OS is a Gentoo linux distro and the complier was updated not to long ago. Based your comments and results I am going to close this issue and go down the OS/compiler route. If anything specific with beast pops up, I will open another issue.   
  
Thank you very much for taking the time to look at this. I really like beast and think its a great library. I think  am gong to work through the contributing guidelines and participate.  Again thank you.
