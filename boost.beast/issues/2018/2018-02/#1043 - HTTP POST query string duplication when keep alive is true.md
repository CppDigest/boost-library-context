# #1043 - HTTP POST query string duplication when keep alive is true [Closed]

> Username: carolinebeltran  
> Created at: 2018-02-27 03:24:08 UTC  
> Updated at: 2018-02-27 18:07:54 UTC  
> Closed at: 2018-02-27 18:07:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1043  

### Version of Beast  
BOOST_BEAST_VERSION 144  
  
### Steps necessary to reproduce the problem  
Simply post an HTML form to a Beast Server configured with 'response.keep_alive(true)'.  
  
### All relevant compiler information  
VC++ 2017 and GCC version 6.3.0  
  
Simple modification to **http_server_async.cpp** to handle HTTP POST.  Unfortunately, setting keep_alive causes your query strings to be **concatenated after the last query string**.  For example:  
  
```  
res.keep_alive(false);	// THIS IS OK, WILL PRODUCE THE FOLLOWING EXPECTED QUERY STRINGS:  
[firstname=Bob&lastname=Smith]  
[firstname=Tom&lastname=Jones]  
[firstname=John&lastname=Doe]  
  
res.keep_alive(false);	// BAD, CONCATENATED QUERY STRING PRODUCED:  
[firstname=Bob&lastname=Smith]  
[firstname=Bob&lastname=Smithfirstname=Tom&lastname=Jones]  
[firstname=Bob&lastname=Smithfirstname=Tom&lastname=Jonesfirstname=John&lastname=Doe]  
```  
  
MY SIMPLE HTML FORM GETTING STRIPPED OUT, SO I REMOVED THE FIRST 'LESS THAN SIGN' FOR EACH HTML TAG.  Simply (add the missing less than tags) then save to html file on your desktop and open with your web browser:  
  
```  
<html>  
<body>  
<form action="http://127.0.0.1:8080/whatever.php" method="POST">  
  First name:<br>  
  <input type="text" name="firstname" value="Mickey">  
  <br>  
  Last name:<br>  
  <input type="text" name="lastname" value="Mouse">  
  <br><br>  
  <input type="submit" value="Submit">  
</form>  
</body>  
</html>  
```  
  
Here is the full source code (VC++/GCC) that demonstrates the problem.  I marked my addition with 'begin post handler' and 'end post handler'.  Other than that, there are no other changes to the Beast demo.  
  
```  
#include "stdafx.h"	// VC++  
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
  
using tcp = boost::asio::ip::tcp;  
namespace http = boost::beast::http;  
  
boost::beast::string_view  
mime_type(boost::beast::string_view path)  
{  
	using boost::beast::iequals;  
	auto const ext = [&path]  
	{  
		auto const pos = path.rfind(".");  
		if (pos == boost::beast::string_view::npos)  
			return boost::beast::string_view{};  
		return path.substr(pos);  
	}();  
	if (iequals(ext, ".htm"))  return "text/html";  
	if (iequals(ext, ".html")) return "text/html";  
	if (iequals(ext, ".php"))  return "text/html";  
	if (iequals(ext, ".css"))  return "text/css";  
	if (iequals(ext, ".txt"))  return "text/plain";  
	if (iequals(ext, ".js"))   return "application/javascript";  
	if (iequals(ext, ".json")) return "application/json";  
	if (iequals(ext, ".xml"))  return "application/xml";  
	if (iequals(ext, ".swf"))  return "application/x-shockwave-flash";  
	if (iequals(ext, ".flv"))  return "video/x-flv";  
	if (iequals(ext, ".png"))  return "image/png";  
	if (iequals(ext, ".jpe"))  return "image/jpeg";  
	if (iequals(ext, ".jpeg")) return "image/jpeg";  
	if (iequals(ext, ".jpg"))  return "image/jpeg";  
	if (iequals(ext, ".gif"))  return "image/gif";  
	if (iequals(ext, ".bmp"))  return "image/bmp";  
	if (iequals(ext, ".ico"))  return "image/vnd.microsoft.icon";  
	if (iequals(ext, ".tiff")) return "image/tiff";  
	if (iequals(ext, ".tif"))  return "image/tiff";  
	if (iequals(ext, ".svg"))  return "image/svg+xml";  
	if (iequals(ext, ".svgz")) return "image/svg+xml";  
	return "application/text";  
}  
  
std::string path_cat(boost::beast::string_view base, boost::beast::string_view path)  
{  
	if (base.empty())  
		return path.to_string();  
	std::string result = base.to_string();  
#if BOOST_MSVC  
	char constexpr path_separator = '\\';  
	if (result.back() == path_separator)  
		result.resize(result.size() - 1);  
	result.append(path.data(), path.size());  
	for (auto& c : result)  
		if (c == '/')  
			c = path_separator;  
#else  
	char constexpr path_separator = '/';  
	if (result.back() == path_separator)  
		result.resize(result.size() - 1);  
	result.append(path.data(), path.size());  
#endif  
	return result;  
}  
  
template<class Body, class Allocator, class Send>  
	void handle_request(	boost::beast::string_view doc_root,  
							http::request<Body, http::basic_fields<Allocator>>&& req,  
							Send&& send)  
{  
	// Returns a bad request response  
	auto const bad_request =  
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
	auto const not_found =  
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
	auto const server_error =  
		[&req](boost::beast::string_view what)  
		{  
			http::response<http::string_body> res{ http::status::internal_server_error, req.version() };  
			res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
			res.set(http::field::content_type, "text/html");  
			res.keep_alive(req.keep_alive());  
			res.body() = "An error occurred: '" + what.to_string() + "'";  
			res.prepare_payload();  
			return res;  
		};  
  
  
	/* --------------------------begin post handler -------------------------------- */  
	// Respond to POST request   
	if (req.method() == http::verb::post)  
	{  
		/* DISPLAY QUERY STRING */  
		std::cout << req.body() << std::endl;  // firstname=Bill&lastname=Smith  
  
		http::response<http::string_body> res;  
  
		/** failed attempt to solve 'keep alive' problem */  
//		res.clear();  
  
		res.result(http::status::ok);  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
  
		/** PROBLEM - setting 'true' causes query string concatenation problem */  
		res.keep_alive(true);	// BAD  
//		res.keep_alive(false);	// OK  
  
		res.body() =  
			"<html>\n"  
			"<head><title>HTTP POST response</title></head>\n"  
			"<body>\n"  
			"<h1>hello world</h1>\n"  
			"</body>\n"  
			"</html>\n";  
  
		res.content_length(res.body().size());  
  
		if (res.keep_alive())	std::cout << "keep alive true" << std::endl;  
		else				std::cout << "keep alive false" << std::endl;  
  
		return send(std::move(res));  
	}  
	/* ---------------------------end post handler --------------------------------- */  
  
	// Make sure we can handle the GET/HEAD methods  
	if (req.method() != http::verb::get && req.method() != http::verb::head)  
		return send(bad_request("Unknown HTTP-method"));  
  
	// Request path must be absolute and not contain "..".  
	if (req.target().empty() || req.target()[0] != '/' ||   
		req.target().find("..") != boost::beast::string_view::npos)  
		return send(bad_request("Illegal request-target"));  
  
	// Build the path to the requested file  
	std::string path = path_cat(doc_root, req.target());  
	if (req.target().back() == '/')  
		path.append("index.html");  
  
	// Attempt to open the file  
	boost::beast::error_code ec;  
	http::file_body::value_type body;  
	body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
  
	// Handle the case where the file doesn't exist  
	if (ec == boost::system::errc::no_such_file_or_directory)  
		return send(not_found(req.target()));  
  
	// Handle an unknown error  
	if (ec)  
		return send(server_error(ec.message()));  
  
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
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void fail(boost::system::error_code ec, char const* what)  
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
  
		explicit send_lambda(session& self) : self_(self)  
		{  
		}  
  
		template<bool isRequest, class Body, class Fields>  
		void operator()(http::message<isRequest, Body, Fields>&& msg) const  
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
			http::async_write(self_.socket_, *sp,  
				boost::asio::bind_executor(  
					self_.strand_,  
					std::bind(  
						&session::on_write,  
						self_.shared_from_this(),  
						std::placeholders::_1,  
						std::placeholders::_2,  
						sp->need_eof())));  
		}  
	};  
  
	tcp::socket socket_;  
	boost::asio::strand<  
		boost::asio::io_context::executor_type> strand_;  
	boost::beast::flat_buffer buffer_;  
	std::string const& doc_root_;  
	http::request<http::string_body> req_;  
	std::shared_ptr<void> res_;  
	send_lambda lambda_;  
  
public:  
	// Take ownership of the socket  
	explicit session(tcp::socket socket, std::string const& doc_root)  
		: socket_(std::move(socket))  
		, strand_(socket_.get_executor())  
		, doc_root_(doc_root)  
		, lambda_(*this)  
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
		http::async_read(socket_, buffer_, req_,  
			boost::asio::bind_executor(  
				strand_,  
				std::bind(  
					&session::on_read,  
					shared_from_this(),  
					std::placeholders::_1,  
					std::placeholders::_2)));  
	}  
  
	void on_read(boost::system::error_code ec, std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		// This means they closed the connection  
		if (ec == http::error::end_of_stream)  
			return do_close();  
  
		if (ec)  
			return fail(ec, "read");  
  
		// Send the response  
		handle_request(doc_root_, std::move(req_), lambda_);  
	}  
  
	void on_write(boost::system::error_code ec, std::size_t bytes_transferred, bool close)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		if (ec)  
			return fail(ec, "write");  
  
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
	std::string const& doc_root_;  
  
public:  
	listener(  
		boost::asio::io_context& ioc,  
		tcp::endpoint endpoint,  
		std::string const& doc_root)  
		: acceptor_(ioc)  
		, socket_(ioc)  
		, doc_root_(doc_root)  
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
  
	// Start accepting incoming connections  
	void run()  
	{  
		if (!acceptor_.is_open())  
			return;  
		do_accept();  
	}  
  
	void do_accept()  
	{  
		acceptor_.async_accept(socket_,  
			std::bind(  
				&listener::on_accept,  
				shared_from_this(),  
				std::placeholders::_1));  
	}  
  
	void on_accept(boost::system::error_code ec)  
	{  
		if (ec)  
		{  
			fail(ec, "accept");  
		}  
		else  
		{  
			// Create the session and run it  
			std::make_shared<session>(  
				std::move(socket_),  
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
	auto const address = boost::asio::ip::make_address(argv[1]);  
	auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
	std::string const doc_root = argv[3];  
	auto const threads = std::max<int>(1, std::atoi(argv[4]));  
  
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
  
	return EXIT_SUCCESS;  
}  
```

---

## Comment 1

> Username: carolinebeltran  
> Created at: 2018-02-27 03:35:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1043#issuecomment-368736381  

I forgot to mention that debugging information will be written to the command prompt (or terminal).  Once you open the html file with your browser, post a few times and you will see the bad output that is written when keep alive is set to true.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-02-27 04:16:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1043#issuecomment-368742005  

Thanks for the detailed report. I was able to build and run the program with no trouble, and I used the HTML form you provided to send a POST to the server. I can reproduce the defect. Well, this is a little embarassing, seems I didn't follow my own documentation. HTTP read operations which accept messages as parameters require a newly constructed message. This is easily accomplished by putting the request in an `optional` type of container and constructing it fresh before each operation. The documentation explains it:  
  
_"This object should not have previous contents, otherwise the behavior is undefined. "_  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__http__read/overload3.html  
  
I'll make sure this gets fixed for Boost 1.67, which is coming out in several weeks.  
  
Note that the problem is not directly cause by `keep_alive`, it just becomes apparent when the request object gets reused. And there is no way to fix it in `handle_request`, it has to be addressed in the `session` object. This also affects every server, since they share a similar structure.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-27 04:27:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1043#issuecomment-368743467  

Please try the **v160** branch here:  
https://github.com/vinniefalco/beast/commits/v160  
  
Reapply your changes, and let me know that it is fixed. I tried it and it worked. This will go into Boost 1.67.0.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-02-27 16:12:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1043#issuecomment-368932245  

Instead of `optional` there is an easier way:  
```  
    req_ = {};  
```

---

## Comment 5

> Username: carolinebeltran  
> Created at: 2018-02-27 18:07:40 UTC  
> Updated at: 2018-02-27 18:07:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1043#issuecomment-368971457  

Hi Vinnie, your empty request fix resolved the problem!  Thank you!
