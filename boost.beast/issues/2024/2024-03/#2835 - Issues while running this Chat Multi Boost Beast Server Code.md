# #2835 - Issues while running this Chat Multi Boost Beast Server Code. [Closed]

> Username: shivank1111  
> Created at: 2024-03-13 12:37:26 UTC  
> Updated at: 2024-03-27 10:58:22 UTC  
> Closed at: 2024-03-27 10:58:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2835  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
Boost version: 1.83.0  
  
### Steps necessary to reproduce the problem  
  
While compiling the code I am getting an error.   
Path to the Github Code:   
"https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi"  
  
### All relevant compiler information  
  
I am getting this error :  
  
" /tmp/cc4R3tP6.o: In function `main':  
main.cpp:(.text+0x193): undefined reference to `listener::run()'  
/tmp/cc4R3tP6.o: In function `boost::detail::sp_if_not_array<shared_state>::type                                                                                         boost::make_shared<shared_state, char*&>(char*&)':  
main.cpp:(.text._ZN5boost11make_sharedI12shared_stateJRPcEEENS_6detail15sp_if_no                                                                                        t_arrayIT_E4typeEDpOT0_[_ZN5boost11make_sharedI12shared_stateJRPcEEENS_6detail15                                                                                        sp_if_not_arrayIT_E4typeEDpOT0_]+0xa1): undefined reference to `shared_state::sh                                                                                        ared_state(std::string)'  
/tmp/cc4R3tP6.o: In function `boost::detail::sp_if_not_array<listener>::type boo                                                                                        st::make_shared<listener, boost::asio::io_context&, boost::asio::ip::basic_endpo                                                                                        int<boost::asio::ip::tcp>, boost::shared_ptr<shared_state> >(boost::asio::io_con                                                                                        text&, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>&&, boost::shared_pt                                                                                        r<shared_state>&&)':  
main.cpp:(.text._ZN5boost11make_sharedI8listenerJRNS_4asio10io_contextENS2_2ip14                                                                                        basic_endpointINS5_3tcpEEENS_10shared_ptrI12shared_stateEEEEENS_6detail15sp_if_n                                                                                        ot_arrayIT_E4typeEDpOT0_[_ZN5boost11make_sharedI8listenerJRNS_4asio10io_contextE                                                                                        NS2_2ip14basic_endpointINS5_3tcpEEENS_10shared_ptrI12shared_stateEEEEENS_6detail                                                                                        15sp_if_not_arrayIT_E4typeEDpOT0_]+0xe0): undefined reference to `listener::list                                                                                        ener(boost::asio::io_context&, boost::asio::ip::basic_endpoint<boost::asio::ip::                                                                                        tcp>, boost::shared_ptr<shared_state> const&)'  
collect2: error: ld returned 1 exit status  
"  
  
  
I have just compiling the exact downloaded file of code from github but still getting this error and I am not able to find the exact solution to correct the error.  
I am using CentOS 7 machine.   
  
Attaching the SS also.  
![Chat_Multi_1](https://github.com/boostorg/beast/assets/160120467/f0dd842f-181c-40e6-a5b2-a8638fdd0b63)  
  
  
Kindly help me with the issue.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-03-13 12:46:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2835#issuecomment-1994320281  

This example is divided into multiple source files; you need to pass them to your build command:  
```BASH  
g++ listener.cpp  main.cpp  shared_state.cpp  websocket_session.cpp -o websocket-chat-multi  
```

---

## Comment 2

> Username: shivank1111  
> Created at: 2024-03-14 04:22:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2835#issuecomment-1996377199  

Hi, Still some error is coming.   
I am compiling through this line:  
"g++ -std=c++11 listener.cpp  main.cpp  shared_state.cpp  websocket_session.cpp http_session.cpp -o websocket-chat-multi"   
  
Attaching the SS for reference.  
![Chat_Multi_2](https://github.com/boostorg/beast/assets/160120467/4dbcfd36-8b61-4399-b731-2c0cb4d39d8c)

---

## Comment 3

> Username: ashtum  
> Created at: 2024-03-14 05:16:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2835#issuecomment-1996540515  

You're encountering a linker error because it couldn't find the libpthread symbols. To resolve this, add `-pthread` to your build command.

---

## Comment 4

> Username: shivank1111  
> Created at: 2024-03-14 11:24:46 UTC  
> Updated at: 2024-03-14 12:20:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2835#issuecomment-1997227311  

Hi,   
This problem is solved but I have new problem, i.e, If I am trying to integrate the whole thing in one program and run then it is giving some errors. I am providing the CPP code of the whole chat-multi integrated code.   
  
```#include <boost/beast.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio.hpp>   
#include <boost/smart_ptr.hpp>  
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
#include <map>  
#include <unordered_map>  
#include <unordered_set>  
#include <vector>  
#include <mutex>  
#include <boost/algorithm/string.hpp>   
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/json_parser.hpp>   
  
  
namespace net = boost::asio;                    // from <boost/asio.hpp>  
namespace beast = boost::beast;                 // from <boost/beast.hpp>  
namespace http = beast::http;                   // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket;         // from <boost/beast/websocket.hpp>  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
  
  
//------------------------------------------------------------------------------  
  
// Return a reasonable mime type based on the extension of a file.  
beast::string_view mime_type(beast::string_view path)  
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
std::string path_cat( beast::string_view base, beast::string_view path)  
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
template <class Body, class Allocator> http::message_generator  
handle_request( beast::string_view doc_root, http::request<Body, http::basic_fields<Allocator>>&& req)  
{  
    // Returns a bad request response  
    auto const bad_request = [&req](beast::string_view why)  
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
    auto const not_found = [&req](beast::string_view target)  
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
    auto const server_error = [&req](beast::string_view what)  
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
    if( req.method() != http::verb::get && req.method() != http::verb::head)  
        return bad_request("Unknown HTTP-method");  
  
    // Request path must be absolute and not contain "..".  
    if( req.target().empty() || req.target()[0] != '/' || req.target().find("..") != beast::string_view::npos)  
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
    if(ec == boost::system::errc::no_such_file_or_directory)  
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
  
// Forward declaration  
class websocket_session;  
//class shared_state;  
//class listener;  
//class http_session;  
  
//------------------------------------------------------------------------------  
  
// Represents the shared server state  
class shared_state: public boost::enable_shared_from_this<shared_state>  
{  
	std::string const doc_root_;  
  
    // This mutex synchronizes all access to sessions_  
    std::mutex mutex_;  
  
    // Keep a list of all the connected clients  
    std::unordered_set<websocket_session*> sessions_;  
  
public:  
    explicit  
    //shared_state(std::string doc_root);  
	shared_state(std::string doc_root) : doc_root_(std::move(doc_root))  
	{  
	}  
  
    std::string const& doc_root() const noexcept  
    {  
        return doc_root_;  
    }  
  
    /*void join  (websocket_session* session);  
    void leave (websocket_session* session);  
    void send  (std::string message);*/  
	  
	void join(websocket_session* session)  
	{  
		std::lock_guard<std::mutex> lock(mutex_);  
		sessions_.insert(session);  
	}  
  
	void leave(websocket_session* session)  
	{  
		std::lock_guard<std::mutex> lock(mutex_);  
		sessions_.erase(session);  
	}  
  
	// Broadcast a message to all websocket client sessions  
	void send(std::string message)  
	{  
		// Put the message in a shared pointer so we can re-use it for each client  
		auto const ss = boost::make_shared<std::string const>(std::move(message));  
  
		// Make a local list of all the weak pointers representing  
		// the sessions, so we can do the actual sending without  
		// holding the mutex:  
		std::vector<boost::weak_ptr<websocket_session>> v;  
		{  
			std::lock_guard<std::mutex> lock(mutex_);  
			v.reserve(sessions_.size());  
			for(auto p : sessions_)  
				v.emplace_back(p->weak_from_this());  
		}  
  
		// For each session in our local list, try to acquire a strong  
		// pointer. If successful, then send the message on that session.  
		for(auto const& wp : v)  
			if(auto sp = wp.lock())  
				sp->send(ss);  
	}  
};  
  
//------------------------------------------------------------------------------  
  
// Represents an active WebSocket connection to the server  
  
class websocket_session : public boost::enable_shared_from_this<websocket_session>  
{  
    beast::flat_buffer buffer_;  
    websocket::stream<beast::tcp_stream> ws_;  
    boost::shared_ptr<shared_state> state_;  
    std::vector<boost::shared_ptr<std::string const>> queue_;  
  
    /*void fail(beast::error_code ec, char const* what);  
    void on_accept(beast::error_code ec);  
    void on_read(beast::error_code ec, std::size_t bytes_transferred);  
    void on_write(beast::error_code ec, std::size_t bytes_transferred);*/  
	  
	void fail(beast::error_code ec, char const* what)  
	{  
		// Don't report these  
		if( ec == net::error::operation_aborted ||  
			ec == websocket::error::closed)  
			return;  
  
		std::cerr << what << ": " << ec.message() << "\n";  
	}  
  
	void on_accept(beast::error_code ec)  
	{  
		// Handle the error, if any  
		if(ec)  
			return fail(ec, "accept");  
  
		// Add this session to the list of active sessions  
		state_->join(this);  
  
		// Read a message  
		ws_.async_read(  
			buffer_,  
			beast::bind_front_handler(  
				&websocket_session::on_read,  
				shared_from_this()));  
	}  
  
	void on_read(beast::error_code ec, std::size_t)  
	{  
		// Handle the error, if any  
		if(ec)  
			return fail(ec, "read");  
  
		// Send to all connections  
		state_->send(beast::buffers_to_string(buffer_.data()));  
  
		// Clear the buffer  
		buffer_.consume(buffer_.size());  
  
		// Read another message  
		ws_.async_read(  
			buffer_,  
			beast::bind_front_handler(  
				&websocket_session::on_read,  
				shared_from_this()));  
	}  
	  
	void on_write(beast::error_code ec, std::size_t)  
	{  
		// Handle the error, if any  
		if(ec)  
			return fail(ec, "write");  
  
		// Remove the string from the queue  
		queue_.erase(queue_.begin());  
  
		// Send the next message if any  
		if(! queue_.empty())  
			ws_.async_write(  
				net::buffer(*queue_.front()),  
				beast::bind_front_handler(  
					&websocket_session::on_write,  
					shared_from_this()));  
	}  
  
  
public:  
    //websocket_session( tcp::socket&& socket, boost::shared_ptr<shared_state> const& state);  
	websocket_session( tcp::socket&& socket, boost::shared_ptr<shared_state> const& state)  
    : ws_(std::move(socket)), state_(state)  
	{  
	}  
  
	//~websocket_session();  
	~websocket_session()  
	{  
		// Remove this session from the list of active sessions  
		state_->leave(this);  
	}  
  
    /*template<class Body, class Allocator>  
    void run(http::request<Body, http::basic_fields<Allocator>> req);  
  
    // Send a message  
    void send(boost::shared_ptr<std::string const> const& ss);*/  
	  
	template<class Body, class Allocator>  
	void run(http::request<Body, http::basic_fields<Allocator>> req)  
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
						" websocket-chat-multi");  
			}));  
  
		// Accept the websocket handshake  
		ws_.async_accept(  
			req,  
			beast::bind_front_handler(  
				&websocket_session::on_accept,  
				shared_from_this()));  
	}  
	  
	void send(boost::shared_ptr<std::string const> const& ss)  
	{  
		// Post our work to the strand, this ensures  
		// that the members of `this` will not be  
		// accessed concurrently.  
  
		net::post(  
			ws_.get_executor(),  
			beast::bind_front_handler(  
				&websocket_session::on_send,  
				shared_from_this(),  
				ss));  
	}  
  
private:  
    //void on_send(boost::shared_ptr<std::string const> const& ss);  
	  
	void on_send(boost::shared_ptr<std::string const> const& ss)  
	{  
		// Always add to queue  
		queue_.push_back(ss);  
  
		// Are we already writing?  
		if(queue_.size() > 1)  
			return;  
  
		// We are not currently writing, so send this immediately  
		ws_.async_write(  
			net::buffer(*queue_.front()),  
			beast::bind_front_handler(  
				&websocket_session::on_write,  
				shared_from_this()));  
	}  
  
};  
  
//------------------------------------------------------------------------------  
// Represents an established HTTP connection  
  
class http_session : public boost::enable_shared_from_this<http_session>  
{  
    beast::tcp_stream stream_;  
    beast::flat_buffer buffer_;  
    boost::shared_ptr<shared_state> state_;  
  
    // The parser is stored in an optional container so we can  
    // construct it from scratch it at the beginning of each new message.  
    boost::optional<http::request_parser<http::string_body>> parser_;  
  
    struct send_lambda;  
  
    /*void fail(beast::error_code ec, char const* what);  
    void do_read();  
    void on_read(beast::error_code ec, std::size_t);  
    void on_write(beast::error_code ec, std::size_t, bool close);*/  
	  
	// Report a failure  
	void fail(beast::error_code ec, char const* what)  
	{  
		// Don't report on canceled operations  
		if(ec == net::error::operation_aborted)  
			return;  
  
		std::cerr << what << ": " << ec.message() << "\n";  
	}  
	  
	void do_read()  
	{  
		// Construct a new parser for each message  
		parser_.emplace();  
  
		// Apply a reasonable limit to the allowed size  
		// of the body in bytes to prevent abuse.  
		parser_->body_limit(10000);  
  
		// Set the timeout.  
		stream_.expires_after(std::chrono::seconds(30));  
  
		// Read a request  
		http::async_read(  
			stream_,  
			buffer_,  
			parser_->get(),  
			beast::bind_front_handler(  
				&http_session::on_read,  
				shared_from_this()));  
	}  
  
	void on_read(beast::error_code ec, std::size_t)  
	{  
		// This means they closed the connection  
		if(ec == http::error::end_of_stream)  
		{  
			stream_.socket().shutdown(tcp::socket::shutdown_send, ec);  
			return;  
		}  
  
		// Handle the error, if any  
		if(ec)  
			return fail(ec, "read");  
  
		// See if it is a WebSocket Upgrade  
		if(websocket::is_upgrade(parser_->get()))  
		{  
			// Create a websocket session, transferring ownership  
			// of both the socket and the HTTP request.  
			boost::make_shared<websocket_session>(  
				stream_.release_socket(),  
					state_)->run(parser_->release());  
			return;  
		}  
  
		// Handle request  
		http::message_generator msg =  
			handle_request(state_->doc_root(), parser_->release());  
  
		// Determine if we should close the connection  
		bool keep_alive = msg.keep_alive();  
  
		auto self = shared_from_this();  
  
		// Send the response  
		beast::async_write(  
			stream_, std::move(msg),  
			[self, keep_alive](beast::error_code ec, std::size_t bytes)  
			{  
				self->on_write(ec, bytes, keep_alive);  
			});  
	}  
  
	void on_write(beast::error_code ec, std::size_t, bool keep_alive)  
	{  
		// Handle the error, if any  
		if(ec)  
			return fail(ec, "write");  
  
		if(! keep_alive)  
		{  
			// This means we should close the connection, usually because  
			// the response indicated the "Connection: close" semantic.  
			stream_.socket().shutdown(tcp::socket::shutdown_send, ec);  
			return;  
		}  
  
		// Read another request  
		do_read();  
	}  
  
	  
public:  
    /*http_session(  
        tcp::socket&& socket,  
        boost::shared_ptr<shared_state> const& state);*/  
	  
	http_session( tcp::socket&& socket, boost::shared_ptr<shared_state> const& state)  
    : stream_(std::move(socket)), state_(state)  
	{  
	}  
	  
    //void run();  
	void run()  
	{  
		do_read();  
	}  
};  
  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public boost::enable_shared_from_this<listener>  
{  
    net::io_context& ioc_;  
    tcp::acceptor acceptor_;  
    boost::shared_ptr<shared_state> state_;  
  
    /*void fail(beast::error_code ec, char const* what);  
    void on_accept(beast::error_code ec, tcp::socket socket);*/  
  
public:  
    //listener( net::io_context& ioc, tcp::endpoint endpoint, boost::shared_ptr<shared_state> const& state);  
	  
	listener( net::io_context& ioc, tcp::endpoint endpoint, boost::shared_ptr<shared_state> const& state)  
    : ioc_(ioc), acceptor_(ioc), state_(state)  
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
		acceptor_.listen( net::socket_base::max_listen_connections, ec);  
		if(ec)  
		{  
			fail(ec, "listen");  
			return;  
		}  
	}  
	  
    // Start accepting incoming connections  
    //void run();  
	  
	void run()  
	{  
		// The new connection gets its own strand  
		acceptor_.async_accept( net::make_strand(ioc_),	beast::bind_front_handler( &listener::on_accept, shared_from_this()));  
	}  
  
	// Report a failure  
	void fail(beast::error_code ec, char const* what)  
	{  
		// Don't report on canceled operations  
		if(ec == net::error::operation_aborted)  
			return;  
		std::cerr << what << ": " << ec.message() << "\n";  
	}  
  
	// Handle a connection  
	void on_accept(beast::error_code ec, tcp::socket socket)  
	{  
		if(ec)  
			return fail(ec, "accept");  
		else  
			// Launch a new session for this connection  
			boost::make_shared<http_session>(  
				std::move(socket),  
				state_)->run();  
  
		// The new connection gets its own strand  
		acceptor_.async_accept(  
			net::make_strand(ioc_),  
			beast::bind_front_handler(  
				&listener::on_accept,  
				shared_from_this()));  
	}  
	  
};  
  
  
//------------------------------------------------------------------------------  
  
  
int main(int argc, char* argv[])  
{  
    // Check command line arguments.  
    if (argc != 5)  
    {  
        std::cerr <<  
            "Usage: websocket-chat-multi <address> <port> <doc_root> <threads>\n" <<  
            "Example:\n" <<  
            "    websocket-chat-server 0.0.0.0 8080 . 5\n";  
        return EXIT_FAILURE;  
    }  
    auto address = net::ip::make_address(argv[1]);  
    auto port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto doc_root = argv[3];  
    auto const threads = std::max<int>(1, std::atoi(argv[4]));  
  
    // The io_context is required for all I/O  
    net::io_context ioc;  
  
    // Create and launch a listening port  
    boost::make_shared<listener>(  
        ioc,  
        tcp::endpoint{address, port},  
        boost::make_shared<shared_state>(doc_root))->run();  
  
    // Capture SIGINT and SIGTERM to perform a clean shutdown  
    net::signal_set signals(ioc, SIGINT, SIGTERM);  
    signals.async_wait(  
        [&ioc](boost::system::error_code const&, int)  
        {  
            // Stop the io_context. This will cause run()  
            // to return immediately, eventually destroying the  
            // io_context and any remaining handlers in it.  
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
  
//------------------------------------------------------------------------------  
  
```  
  
  
The issue I am facing is due to placement of "websocket_session" class. It can be solved by adding websocket.hpp file but I need to know in the same cpp file because it will be easy to maintain and run. There are additional issues also I am attaching the SS of the issue. Kindly help me with the same.   
![Chat_Multi_3](https://github.com/boostorg/beast/assets/160120467/4b224bf1-1852-4073-88cc-ed480116dc91)

---

## Comment 5

> Username: shivank1111  
> Created at: 2024-03-27 10:58:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2835#issuecomment-2022480132  

We need to create the shared_state.hpp and websocket_session.hpp files along with main.cpp file. So, like I tried to compile the whole code in the single file is wrong. Because both are called simultaneously.   
  
So, I am closing this issue. If anyone ever finds the solution to put the whole code in one single file of code kindly reopen and provide the solution for the same
