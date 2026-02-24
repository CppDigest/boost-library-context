# #990 - Getting the request body for a request after calling session::on_read() (async server example) [Closed]

> Username: DragonOsman  
> Created at: 2018-01-18 22:50:45 UTC  
> Updated at: 2018-05-08 00:51:33 UTC  
> Closed at: 2018-02-06 19:33:49 UTC  
> Url: https://github.com/boostorg/beast/issues/990  

This is my `main()` function:  
```cpp  
int main(int argc, char* argv[])  
{  
	if (argc < 5)  
	{  
		std::cerr << "Usage: application.exe <address> <port> <doc_root> <threads>\n" <<  
			"Example:\n" <<  
			"    application.exe 0.0.0.0 8080 . 1\n";  
		return EXIT_FAILURE;  
	}  
	const auto address = boost::asio::ip::make_address(argv[1]);  
	const auto port = static_cast<unsigned short>(std::atoi(argv[2]));  
	const std::string doc_root = argv[3];  
	const auto threads = std::max<int>(1, std::atoi(argv[4]));  
  
	LoaderFile loader;  
	Template t{ loader };  
	t.load("index.html");  
	t.set("title", "DragonOsman Currency Converter");  
  
	// The io_context is required for all I/O  
	boost::asio::io_context ioc{ threads };  
  
	// Create and launch a listening port  
	std::make_shared<listener>(ioc, tcp::endpoint{ address, port }, doc_root)->run();  
  
	// Run the I/O service on the requested number of threads  
	std::vector<std::thread> v;  
	v.reserve(threads - 1);  
	for (auto i = threads - 1; i > 0; --i)  
		v.emplace_back(  
			[&ioc]  
	{  
		ioc.run();  
	});  
	ioc.run();  
  
	boost::asio::ip::tcp::socket socket{ ioc };  
	session cur_session{ std::move(socket), doc_root };  
  
	boost::system::error_code ec{};  
	std::size_t bytes_transferred{};  
	cur_session.on_read(ec, bytes_transferred);  
  
	  
}  
```  
And this is my server code, which I made by modifying the `http_server_async.cpp` example:  
`http_server.h`:  
```cpp  
#ifndef HTTP_SERVER_H  
#define HTTP_SERVER_H  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/ip/tcp.hpp>  
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
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
										// Return a reasonable mime type based on the extension of a file.  
boost::beast::string_view mime_type(boost::beast::string_view path);  
  
// Append an HTTP rel-path to a local filesystem path.  
// The returned path is normalized for the platform.  
std::string path_cat(boost::beast::string_view base, boost::beast::string_view path);  
  
// This function produces an HTTP response for the given  
// request. The type of the response object depends on the  
// contents of the request, so the interface requires the  
// caller to pass a generic lambda for receiving the response.  
template<class Body, class Allocator, class Send>  
void handle_request(boost::beast::string_view doc_root, http::request<Body, http::basic_fields<Allocator>>&& req, Send&& send)  
{  
	// Returns a bad request response  
	const auto bad_request =  
		[&req](boost::beast::string_view why)  
	{  
		http::response<http::string_body> res{ http::status::bad_request, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = why.to_string();  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a not found response  
	const auto not_found =  
		[&req](boost::beast::string_view target)  
	{  
		http::response<http::string_body> res{ http::status::not_found, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "The resource '" + target.to_string() + "' was not found.";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a server error response  
	auto const server_error = [&req](boost::beast::string_view what)  
	{  
		http::response<http::string_body> res{ http::status::internal_server_error, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "An error occurred: '" + what.to_string() + "'";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Make sure we can handle the method  
	if (req.method() != http::verb::get &&  
		req.method() != http::verb::head &&  
		req.method() != http::verb::post)  
	{  
		return send(bad_request("Unknown HTTP-method"));  
	}  
  
	// Request path must be absolute and not contain "..".  
	if (req.target().empty() ||  
		req.target()[0] != '/' ||  
		req.target().find("..") != boost::beast::string_view::npos)  
	{  
		return send(bad_request("Illegal request-target"));  
	}  
  
	// Build the path to the requested file  
	std::string path = path_cat(doc_root, req.target());  
	if (req.target().back() == '/')  
	{  
		path.append("index.html");  
	}  
  
	// In case of POST request, check the path URI  
	else if (req.target().front() == '/' && req.target().size() > 1)  
	{  
		using boost::beast::iequals;  
		const auto ext = [&path]  
		{  
			auto pos = path.rfind(".");  
			if (pos == boost::beast::string_view::npos)  
			{  
				return boost::beast::string_view{};  
			}  
			pos = path.find(" ");  
			if (pos != boost::beast::string_view::npos)  
			{  
				return boost::beast::string_view{};  
			}  
			return boost::beast::string_view(path.substr(pos));  
		}();  
		if (!iequals(ext, ".cgi") && !iequals(ext, ".exe"))  
		{  
			return send(bad_request("Illegal request-target"));  
		}  
	}  
  
	// Attempt to open the file  
	boost::beast::error_code ec;  
	http::file_body::value_type body;  
	body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
  
	// Handle the case where the file doesn't exist  
	if (ec == boost::system::errc::no_such_file_or_directory)  
	{  
		return send(not_found(req.target()));  
	}  
  
	// Handle an unknown error  
	if (ec)  
	{  
		return send(server_error(ec.message()));  
	}  
  
	// Respond to HEAD request  
	if (req.method() == http::verb::head)  
	{  
		http::response<http::empty_body> res{ http::status::ok, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to GET request  
	else if (req.method() == http::verb::get)  
	{  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		boost::beast::string_view content_type = req[http::field::content_type];  
		if (content_type != "multipart/form-data" && content_type != "application/x-www-form-urlencoded")  
		{  
			return send(bad_request("Bad request"));  
		}  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
	return send(bad_request("Bad request"));  
}  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void fail(boost::system::error_code ec, const char *what);  
  
// Handles an HTTP server connection  
class session : public std::enable_shared_from_this<session>  
{  
	// This is the C++11 equivalent of a generic lambda.  
	// The function object is used to send an HTTP message.  
	struct send_lambda  
	{  
		session& self_;  
  
		explicit send_lambda(session& self)  
			: self_{ self }  
		{  
		}  
  
		template<bool isRequest, class Body, class Fields>  
		void operator()(http::message<isRequest, Body, Fields>&& msg) const  
		{  
			// The lifetime of the message has to extend  
			// for the duration of the async operation so  
			// we use a shared_ptr to manage it.  
			auto sp = std::make_shared<http::message<isRequest, Body, Fields>>(std::move(msg));  
  
			// Store a type-erased version of the shared  
			// pointer in the class to keep it alive.  
			self_.res_ = sp;  
  
			// Write the response  
			http::async_write(self_.socket_, *sp, boost::asio::bind_executor(self_.strand_, std::bind(&session::on_write,  
				self_.shared_from_this(), std::placeholders::_1, std::placeholders::_2, sp->need_eof())));  
		}  
	};  
  
	tcp::socket socket_;  
	boost::asio::strand<boost::asio::io_context::executor_type> strand_;  
	boost::beast::flat_buffer buffer_;  
	std::string const& doc_root_;  
	http::request<http::string_body> req_;  
	std::shared_ptr<void> res_;  
	send_lambda lambda_;  
  
public:  
	// Take ownership of the socket  
	explicit session(tcp::socket socket, const std::string &doc_root)  
		: socket_{ std::move(socket) }  
		, strand_{ socket_.get_executor() }  
		, doc_root_{ doc_root }  
		, lambda_{ *this }  
	{  
	}  
  
	// Start the asynchronous operation  
	void run()  
	{  
		do_read();  
	}  
  
	void do_read()  
	{  
		// Read a request  
		http::async_read(socket_, buffer_, req_, boost::asio::bind_executor(strand_, std::bind(&session::on_read, shared_from_this(),  
			std::placeholders::_1, std::placeholders::_2)));  
	}  
  
	void on_read(boost::system::error_code ec, std::size_t bytes_transferred);  
  
	void on_write(boost::system::error_code ec, std::size_t bytes_transferred, bool close);  
  
	void do_close()  
	{  
		// Send a TCP shutdown  
		boost::system::error_code ec;  
		socket_.shutdown(tcp::socket::shutdown_send, ec);  
  
		// At this point the connection is closed gracefully  
	}  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
	tcp::acceptor acceptor_;  
	tcp::socket socket_;  
	const std::string &doc_root_;  
  
public:  
	listener(boost::asio::io_context& ioc, tcp::endpoint endpoint, const std::string &doc_root);  
  
	// Start accepting incoming connections  
	void run()  
	{  
		if (!acceptor_.is_open())  
		{  
			return;  
		}  
		do_accept();  
	}  
  
	void do_accept()  
	{  
		acceptor_.async_accept(socket_, std::bind(&listener::on_accept, shared_from_this(), std::placeholders::_1));  
	}  
  
	void on_accept(boost::system::error_code ec);  
};  
  
#endif  
```  
And `http_server.cpp`:  
```cpp  
#include "http_server.h"  
#include <boost/beast/core.hpp>  
  
void fail(boost::system::error_code ec, const char *what)  
{  
	std::cerr << what << ": " << ec.message() << "\n";  
}  
  
boost::beast::string_view mime_type(boost::beast::string_view path)  
{  
	using boost::beast::iequals;  
	const auto ext = [&path]  
	{  
		const auto pos = path.rfind(".");  
		if (pos == boost::beast::string_view::npos)  
		{  
			return boost::beast::string_view{};  
		}  
		return path.substr(pos);  
	}();  
	if (iequals(ext, ".htm"))  
	{  
		return "text/html";  
	}  
	if (iequals(ext, ".html"))  
	{  
		return "text/html";  
	}  
	if (iequals(ext, ".php"))  
	{  
		return "text/html";  
	}  
	if (iequals(ext, ".css"))  
	{  
		return "text/css";  
	}  
	if (iequals(ext, ".txt"))  
	{  
		return "text/plain";  
	}  
	if (iequals(ext, ".js"))  
	{  
		return "application/javascript";  
	}  
	if (iequals(ext, ".json"))  
	{  
		return "application/json";  
	}  
	if (iequals(ext, ".xml"))  
	{  
		return "application/xml";  
	}  
	if (iequals(ext, ".swf"))  
	{  
		return "application/x-shockwave-flash";  
	}  
	if (iequals(ext, ".flv"))  
	{  
		return "video/x-flv";  
	}  
	if (iequals(ext, ".png"))  
	{  
		return "image/png";  
	}  
	if (iequals(ext, ".jpe"))  
	{  
		return "image/jpeg";  
	}  
	if (iequals(ext, ".jpeg"))  
	{  
		return "image/jpeg";  
	}  
	if (iequals(ext, ".jpg"))  
	{  
		return "image/jpeg";  
	}  
	if (iequals(ext, ".gif"))  
	{  
		return "image/gif";  
	}  
	if (iequals(ext, ".bmp"))  
	{  
		return "image/bmp";  
	}  
	if (iequals(ext, ".ico"))  
	{  
		return "image/vnd.microsoft.icon";  
	}  
	if (iequals(ext, ".tiff"))  
	{  
		return "image/tiff";  
	}  
	if (iequals(ext, ".tif"))  
	{  
		return "image/tiff";  
	}  
	if (iequals(ext, ".svg"))  
	{  
		return "image/svg+xml";  
	}  
	if (iequals(ext, ".svgz"))  
	{  
		return "image/svg+xml";  
	}  
	return "application/text";  
}  
  
std::string path_cat(boost::beast::string_view base, boost::beast::string_view path)  
{  
	if (base.empty())  
	{  
		return path.to_string();  
	}  
	std::string result = base.to_string();  
#if BOOST_MSVC  
	constexpr char path_separator = '\\';  
	if (result.back() == path_separator)  
	{  
		result.resize(result.size() - 1);  
	}  
	result.append(path.data(), path.size());  
	for (auto& c : result)  
	{  
		if (c == '/')  
		{  
			c = path_separator;  
		}  
	}  
#else  
	char constexpr path_separator = '/';  
	if (result.back() == path_separator)  
		result.resize(result.size() - 1);  
	result.append(path.data(), path.size());  
#endif  
	return result;  
}  
  
void session::on_read(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	// This means they closed the connection  
	if (ec == http::error::end_of_stream)  
	{  
		return do_close();  
	}  
  
	if (ec)  
	{  
		return fail(ec, "read");  
	}  
  
	// Send the response  
	handle_request(doc_root_, std::move(req_), lambda_);  
}  
  
void session::on_write(boost::system::error_code ec, std::size_t bytes_transferred, bool close)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	if (ec)  
	{  
		return fail(ec, "write");  
	}  
  
	if (close)  
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
  
listener::listener(boost::asio::io_context& ioc, tcp::endpoint endpoint, const std::string &doc_root)  
	: acceptor_{ ioc }  
	, socket_{ ioc }  
	, doc_root_{ doc_root }  
{  
	boost::system::error_code ec;  
  
	// Open the acceptor  
	acceptor_.open(endpoint.protocol(), ec);  
	if (ec)  
	{  
		fail(ec, "open");  
		return;  
	}  
  
	// Bind to the server address  
	acceptor_.bind(endpoint, ec);  
	if (ec)  
	{  
		fail(ec, "bind");  
		return;  
	}  
  
	// Start listening for connections  
	acceptor_.listen(boost::asio::socket_base::max_listen_connections, ec);  
	if (ec)  
	{  
		fail(ec, "listen");  
		return;  
	}  
}  
  
void listener::on_accept(boost::system::error_code ec)  
{  
	if (ec)  
	{  
		fail(ec, "accept");  
	}  
	else  
	{  
		// Create the session and run it  
		std::make_shared<session>(std::move(socket_), doc_root_)->run();  
	}  
  
	// Accept another connection  
	do_accept();  
}  
```  
Now, as the title says, I need to know how to get the request body now.  Any help would be greatly appreciated.  Thanks in advance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-19 00:21:30 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-358826640  

Do you understand asynchronous Asio programming?

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-01-19 14:22:40 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-358979075  

I looked at the documentation for a bit and did some tutorials, at least.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-19 15:20:14 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-358995385  

Try using the synchronous client instead, it will be easier to understand.

---

## Comment 4

> Username: DragonOsman  
> Created at: 2018-01-19 22:33:55 UTC  
> Updated at: 2018-01-19 22:37:09 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-359108498  

The example I'm using the asynchronous server one, though, not the asynchronous client.    
  
If I can understand it better if I can understand the asynchronous model of Asio, then I'll try studying it more.  I want to use the asynchronous server because I don't want blocking requests.    
  
Edit: I'll have to read about asynchronous programming first and I'm not sure where to start.  So I guess I'll try the synchronous server for now after all.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-01-20 04:05:07 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-359143393  

My apologies, I misread. The asynchronous server is what you want.  
  
You are trying to do things after the call to `io_context::run()`:  
  
```  
  
	// Run the I/O service on the requested number of threads  
	std::vector<std::thread> v;  
	v.reserve(threads - 1);  
	for (auto i = threads - 1; i > 0; --i)  
		v.emplace_back(  
			[&ioc]  
	{  
		ioc.run(); // <---- HERE  
	});  
	ioc.run(); // <---- HERE   
  
        // CODE FROM HERE DOWN WILL NOT EXECUTE  
  
	boost::asio::ip::tcp::socket socket{ ioc };  
	session cur_session{ std::move(socket), doc_root };  
  
	boost::system::error_code ec{};  
	std::size_t bytes_transferred{};  
	cur_session.on_read(ec, bytes_transferred);  
```  
  
Did you notice that none of your code at the bottom of `main` executes? That is because `run` only returns when there is no more work. And as long as you have a listening socket, there's work. I've said a few times that you need to process the request in the function `handle_request` starting from an unmodified copy of the example asynchronous server.  
  
I repeat, process the incoming request in `handle_request`. Do you understand?

---

## Comment 6

> Username: DragonOsman  
> Created at: 2018-01-20 12:38:10 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-359168467  

Thanks.  
  
So if I need to process the incoming request in `handle_request`, and what I need to for the app I'm making now is to process input data from a form where the user submits a money amount and the to- and from-currencies, is this already enough for that?   
```cpp  
template<class Body, class Allocator, class Send>  
void handle_request(boost::beast::string_view doc_root, http::request<Body, http::basic_fields<Allocator>>&& req, Send&& send)  
{  
	// Returns a bad request response  
	const auto bad_request =  
		[&req](boost::beast::string_view why)  
	{  
		http::response<http::string_body> res{ http::status::bad_request, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = why.to_string();  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a not found response  
	const auto not_found =  
		[&req](boost::beast::string_view target)  
	{  
		http::response<http::string_body> res{ http::status::not_found, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "The resource '" + target.to_string() + "' was not found.";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a server error response  
	auto const server_error = [&req](boost::beast::string_view what)  
	{  
		http::response<http::string_body> res{ http::status::internal_server_error, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "An error occurred: '" + what.to_string() + "'";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Make sure we can handle the method  
	if (req.method() != http::verb::get &&  
		req.method() != http::verb::head &&  
		req.method() != http::verb::post)  
	{  
		return send(bad_request("Unknown HTTP-method"));  
	}  
  
	// Request path must be absolute and not contain "..".  
	if (req.target().empty() ||  
		req.target()[0] != '/' ||  
		req.target().find("..") != boost::beast::string_view::npos)  
	{  
		return send(bad_request("Illegal request-target"));  
	}  
  
	// Build the path to the requested file  
	std::string path = path_cat(doc_root, req.target());  
	if (req.target().back() == '/')  
	{  
		path.append("index.html");  
	}  
  
	// In case of POST request, check the path URI  
	else if (req.target().front() == '/' && req.target().size() > 1)  
	{  
		using boost::beast::iequals;  
		const auto ext = [&path]  
		{  
			auto pos = path.rfind(".");  
			if (pos == boost::beast::string_view::npos)  
			{  
				return boost::beast::string_view{};  
			}  
			pos = path.find(" ");  
			if (pos != boost::beast::string_view::npos)  
			{  
				return boost::beast::string_view{};  
			}  
			return boost::beast::string_view(path.substr(pos));  
		}();  
		if (!iequals(ext, ".cgi") && !iequals(ext, ".exe"))  
		{  
			return send(bad_request("Illegal request-target"));  
		}  
	}  
  
	// Attempt to open the file  
	boost::beast::error_code ec;  
	http::file_body::value_type body;  
	body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
  
	// Handle the case where the file doesn't exist  
	if (ec == boost::system::errc::no_such_file_or_directory)  
	{  
		return send(not_found(req.target()));  
	}  
  
	// Handle an unknown error  
	if (ec)  
	{  
		return send(server_error(ec.message()));  
	}  
  
	// Respond to HEAD request  
	if (req.method() == http::verb::head)  
	{  
		http::response<http::empty_body> res{ http::status::ok, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to GET request  
	else if (req.method() == http::verb::get)  
	{  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		boost::beast::string_view content_type = req[http::field::content_type];  
		if (content_type != "multipart/form-data" && content_type != "application/x-www-form-urlencoded")  
		{  
			return send(bad_request("Bad request"));  
		}  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
	return send(bad_request("Bad request"));  
}  
```  
Or is there more I still need to do?  Sorry if this is a stupid question.

---

## Comment 7

> Username: DragonOsman  
> Created at: 2018-01-20 19:46:51 UTC  
> Updated at: 2018-01-20 19:54:17 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-359197195  

This is my current `main` (I put the code that was after `run` before it instead):  
```cpp  
int main(int argc, char* argv[])  
{  
	if (argc < 5)  
	{  
		std::cerr << "Usage: application.exe <address> <port> <doc_root> <threads>\n" <<  
			"Example:\n" <<  
			"    application.exe 0.0.0.0 8080 . 1\n";  
		return EXIT_FAILURE;  
	}  
	const auto address = boost::asio::ip::make_address(argv[1]);  
	const auto port = static_cast<unsigned short>(std::atoi(argv[2]));  
	const std::string doc_root = argv[3];  
	const auto threads = std::max<int>(1, std::atoi(argv[4]));  
  
	LoaderFile loader;  
	Template t{ loader };  
	t.load("index.html");  
	t.set("title", "DragonOsman Currency Converter");  
  
	// The io_context is required for all I/O  
	boost::asio::io_context ioc{ threads };  
  
	boost::asio::ip::tcp::socket socket{ ioc };  
	session cur_session{ std::move(socket), doc_root };  
  
	boost::system::error_code ec{};  
	std::size_t bytes_transferred{};  
	cur_session.on_read(ec, bytes_transferred);  
  
	// Create and launch a listening port  
	std::make_shared<listener>(ioc, tcp::endpoint{ address, port }, doc_root)->run();  
  
	// Run the I/O service on the requested number of threads  
	std::vector<std::thread> v;  
	v.reserve(threads - 1);  
	for (auto i = threads - 1; i > 0; --i)  
		v.emplace_back(  
			[&ioc]  
	{  
		ioc.run();  
	});  
	ioc.run();  
}  
```  
It's when I'm trying to handle the request in `handle_request` that I need to access the request body, right?  So where do I call `request.body` for that, then?  And I have to do all of the stuff in that function without sending anything to my `main` function?  
  
Edit: Also, I still don't really understand how to access the body of the request I got back from the client in `handle_request`.  What do I need to do to access `message::body` in there?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-01-20 21:21:59 UTC  
> Updated at: 2018-01-20 21:22:06 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-359203093  

The body can be accessed by calling `req.body()`

---

## Comment 9

> Username: DragonOsman  
> Created at: 2018-01-21 14:00:13 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-359250493  

How do I parse it after that?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-01-21 14:05:17 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-359250853  

>How do I parse it after that?  
  
You're asking questions which are far beyond the scope of Beast. For learning general programming and algorithms I suggest consulting some literature or online classes. "The Art of Computer Programming" by Donald Knuth is an excellent series, and I believe some of the books are available for free online in PDF form.  
  
You might also consider taking on a project which is a better for your current skill set. Instead of asking "how do I...?" try asking "what do I know how to...?" Examine the abilities with which you are currently proficient (or will be soon, or with a reasonably brief period of study) and choose a project which is a better match for what you can realistically accomplish without depending entirely on mentorship.  
  
Another possibility is to find a mentor who can provide ongoing tutelage and guidance for your project. Unfortunately I know of no such individual but you might try your local university or place of employment (if you work in the industry).

---

## Comment 11

> Username: DragonOsman  
> Created at: 2018-01-25 12:41:37 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-360455672  

I think I can parse a request body of type `string_body` just like I would a normal string so never mind that for now.    
  
Anyway, is it possible to get JSON data back as a response from a GET request?  The currency API I want to query gives back a response as JSON data, so I need to store it in a JSON object.  I'm going to do this in a cache wrapper class so I can cache the API results.

---

## Comment 12

> Username: DragonOsman  
> Created at: 2018-01-25 14:58:01 UTC  
> Updated at: 2018-01-25 15:02:05 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-360490770  

And this is what I have for the cache storage class itself (I'm showing it so I ask if there are problems I need to fix before I can send a GET request to the URL `https://openexchangerates.org/api/latest.json?app_id=MY_APP_ID&symbols=PKR&prettyprint=true&show_alternative=false` (the symbol's value depends on what currency is used in the user's geolocation-returned area - mine is currently in Pakistan so that's why for me it's PKR)):  
  
```cpp  
// This class represents a cache for storing results from the   
// currency exchange API used by openexchangerates.org  
template<typename Query_Data_T, typename Query_Response_T>  
class cache_storage  
{  
public:  
	  
	cache_storage(const std::chrono::duration<std::chrono::seconds> &duration, const tcp::socket &socket)  
		: m_cache{}, m_duration{ duration }, m_socket{ socket }  
	{  
	}  
  
	// This function queries the currency API after making sure  
	// that the stored result(s) is/are old enough  
	const Query_Response_T &query(Query_Data_T query_data)  
	{  
		std::string host{ "https://openexchangerates.org/" };  
		std::string api_endpoint{ "/api/latest.json" };  
		std::string app_id{ "MY_APP_ID" };  
		std::string symbol{ query_data };  
		std::string prettyprint{ "true" };  
		std::string show_alternative{ "false" };  
		std::string query_url =   
			host + api_endpoint + "?app_id=" + app_id + "&symbols=" +   
			symbol + "&prettyprint=" + prettyprint + "&show_alternative=" + show_alternative;  
  
  
	}  
  
private:  
	std::unordered_map<Query_Data_T, std::pair<std::chrono::time_point<std::chrono::steady_clock>, Query_Response_T>> m_cache;  
	std::chrono::duration<std::chrono::seconds> m_duration;  
	tcp::socket m_socket;  
};  
```  
In the examples, I see that the `socket` objects are default constructed.  If that's how it's supposed to be, I'll take out the initializer from the `m_socket` object and just let the class default construct it (should I do that after all?).  And should I also add in an `io_context` data member initialized by `m_socket while I'm at it`?    
  
I need to know if I can receive a JSON object as a response directly when using Beast.  Do I have to create my own custom `body_type` for the JSON object and put the JSON response in that?  And I'm assuming that the way to send and receive the message for this would be the same as in the synchronous HTTP client example (http_client_sync.cpp), but is that correct?  
  
Also, for the URL itself, the part up to "/api" is the API base path and, in my case, "/latest.json" is the endpoint I want since that would give me the exchange rate I want for the symbol(s) I query it for.  What part of the URL I have in the code I showed is the `target`?  Would that be the endpoint with the parameters and values included?

---

## Comment 13

> Username: DragonOsman  
> Created at: 2018-02-01 01:05:14 UTC  
> Updated at: 2018-02-01 01:06:17 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-362124137  

Okay, never mind about all of the above for now.  I'm getting a `std::bad_weak_ptr` exception on the   
  
```cpp  
std::make_shared<listener>(ioc, tcp::endpoint{ address, port }, doc_root)->run();  
```   
line.  I copied that as is from the example, though.  So what do I do to fix it?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2018-02-01 02:48:50 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-362141680  

Are you attempting to call `shared_from_this` in the constructor?

---

## Comment 15

> Username: DragonOsman  
> Created at: 2018-02-01 11:25:34 UTC  
> Updated at: 2018-02-04 19:25:35 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-362237654  

Sorry, I misunderstood.  The exception is being triggered in this code (hence why I put a try-catch block in there):  
```cpp  
template<bool isRequest, class Body, class Fields>  
void operator()(http::message<isRequest, Body, Fields>&& msg) const  
{  
	// The lifetime of the message has to extend  
	// for the duration of the async operation so  
	// we use a shared_ptr to manage it.  
	auto sp = std::make_shared<http::message<isRequest, Body, Fields>>(std::move(msg));  
  
	// Store a type-erased version of the shared  
	// pointer in the class to keep it alive.  
	self_.res_ = sp;  
  
	try  
	{  
		// Write the response  
		http::async_write(self_.socket_, *sp, boost::asio::bind_executor(self_.strand_, std::bind(&session::on_write,  
			self_.shared_from_this(), std::placeholders::_1, std::placeholders::_2, sp->need_eof())));  
	}  
	catch (const std::exception &e)  
	{  
		std::cerr << "Error: " << e.what() << '\n';  
	}  
}  
```  
Is there an easy way to fix this?  
  
Edit: Putting all of the code in one file also didn't take away the exception.

---

## Comment 16

> Username: DragonOsman  
> Created at: 2018-02-02 21:54:31 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-362718890  

The code is in here: https://gist.github.com/DragonOsman/9a7d4ee116efc575dece7d6de46bd31d  
  
What could the reason for the exception be?

---

## Comment 17

> Username: DragonOsman  
> Created at: 2018-02-04 14:24:32 UTC  
> Updated at: 2018-02-04 14:27:36 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-362910343  

I wonder if it'll be fixed if I put all of the code in one file, namely the same one that `main` is in.    
  
Anyway, how can I expect the request body to be structured when I get it in `handle_request`?  The form I have (which I generate using JavaScript) is something like this:  
```html  
<form method="post" action="0.0.0.0/server_exec/currency_converter.exe">  
  <p>With current API plan, changing base currency is not allowed. Sorry for inconvenience.<p><br />  
  <input type="text" name="currency_amount" value="Amount" /><br />  
  <label>From: </label>  
  <select name="from_currency" id="from_currency" value="From">  
   ("option" elements for each currency here, with "id" and "value" attributes with the same value as the currency abbreviation and inner HTML structured as "currency abbreviation - currency name" e.g. "USD - United States Dollar")  
  </select><br />  
  <label>To: </label>  
  <select name="to_currency" id="to_currency" value="From">  
    (same as above)  
  <select>  
  <input type="submit" name="submit" value="Convert" />  
</form>  
```  
So yeah, with a form like this, how can I expect the request to be structured?  Will it be like this?  
```  
From currency: USD - United States Dollar  
To currency: PKR - Pakistani Rupee  
Amount: 100.00  
```  
Or will I have to put the labels there myself if I want them?

---

## Comment 18

> Username: DragonOsman  
> Created at: 2018-02-04 21:34:53 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-362941796  

Commenting out this code in my `main` caused the exception to go away:  
```cpp  
boost::asio::ip::tcp::socket socket{ ioc };  
session cur_session{ std::move(socket), doc_root };  
  
boost::system::error_code ec{};  
std::size_t bytes_transferred{};  
cur_session.on_read(ec, bytes_transferred);  
```  
How do I call `session::on_read` without raising an exception, then?

---

## Comment 19

> Username: DragonOsman  
> Created at: 2018-02-04 23:55:18 UTC  
> Updated at: 2018-05-08 00:51:33 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-362951186  

I just made this change in the code:  
```cpp  
boost::asio::ip::tcp::socket socket{ ioc };  
std::shared_ptr<session> cur_session = std::make_shared<session>(std::move(socket), doc_root);  
  
boost::system::error_code ec{};  
std::size_t bytes_transferred{};  
cur_session->on_read(ec, bytes_transferred);  
```  
Now I have the error ```write: The file handle supplied is not valid```, probably coming from here:  
```cpp  
const json &cache_storage::query(const std::string &query_data)  
{  
	auto found = m_cache.find(query_data);  
	if (found == m_cache.end() || (std::chrono::steady_clock::now() - found->second.first) > m_duration)  
	{  
		std::string host{ "https://openexchangerates.org/" }, api_endpoint{ "/api/latest.json" },  
			app_id{ APP_ID }, symbol{ query_data }, prettyprint{ "true" }, show_alternative{ "false" },  
			target = api_endpoint + "?app_id=" + app_id + "&symbols=" + symbol + "&prettyprint=" + prettyprint + "&show_alternative=" + show_alternative,  
			port{ "443" };  
		int version = 11;  
  
		tcp::resolver resolver{ m_ioc };  
		const auto results = resolver.resolve(host, port);  
		boost::asio::connect(m_socket, results.begin(), results.end());  
  
		http::response<http::string_body> res;  
  
		// Set up an HTTP GET request message  
		http::request<http::string_body> req{ http::verb::get, target, version };  
		req.set(http::field::host, host);  
		req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
		// Send the HTTP request to the remote host  
		http::write(m_socket, req);  
  
		boost::beast::flat_buffer buffer;  
		http::read(m_socket, buffer, res);  
		found = m_cache.insert_or_assign(found, query_data, std::make_pair(std::chrono::steady_clock::now(), json::parse(res.body())));  
	}  
	return found->second.second;  
}  
```  
But then I'm also not calling this anywhere yet since I want to figure how the request body being received in `handle_request` is structured so that I know what to look for.  Then I call that function in `convert` so I can make and cache the request to the currency API I want to get the conversion rates from.    
  
Do I need to close the socket in this function, by the way?  That might not be what's causing the problem, but I do think I need to close the socket somewhere.

---

## Comment 20

> Username: DragonOsman  
> Created at: 2018-02-05 11:06:54 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-363053042  

The error is still persisting, but I'm not familiar with how to handle network errors like this.    
  
Never mind about the request body structure, though; I'll just print the body (once I manage to debug a little exception problem that triggered `abort()` in VS2017).

---

## Comment 21

> Username: DragonOsman  
> Created at: 2018-02-06 14:40:38 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-363442151  

Okay, I've scrapped the async example and am going with the sync one for now.  It works almost perfectly, but I have two errors in the browser's console.  One seems to be something with MS Edge itself so I won't bother with that here.  The second one is what I'm concerned with more.  It says `SCRIPT5009: SCRIPT5009: 'require' is not defined`.  Could this be a peculiarity with C++?  Do I have to write code especially to handle this?    
  
Code: https://gist.github.com/DragonOsman/9a7d4ee116efc575dece7d6de46bd31d    
Just for reference.

---

## Comment 22

> Username: DragonOsman  
> Created at: 2018-02-06 19:33:44 UTC  
> Url: https://github.com/boostorg/beast/issues/990#issuecomment-363538795  

Okay, never mind.  I think I can solve the request problem once I can print out the body and look at it.  I have another problem I need to ask about, but it's unrelated with the request body parsing.  I'll open and issue and close this one.  Sorry to keep bothering you guys (if I am).
