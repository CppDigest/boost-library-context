# #826 - How can I only get the response's body without the header? [Closed]

> Username: aikuimail  
> Created at: 2017-10-23 01:49:28 UTC  
> Updated at: 2017-11-18 20:55:17 UTC  
> Closed at: 2017-11-18 20:55:17 UTC  
> Url: https://github.com/boostorg/beast/issues/826  

`#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
										// Performs an HTTP GET and prints the response  
int main(int argc, char** argv)  
{  
	try  
	{  
		// Check command line arguments.  
		auto const host = "192.168.31.163";  
		auto const port = "38888";  
		auto const target = "/api?";  
		std::string paramOn = "c={\"m\":\"on\",\"dt\":{\"ip\":\"192.168.31.176\",\"pcno\":\"DESKTOP-JOSN7F6\",\"netbarid\":\"33\",\"sessionid\":\"815285d7-1ac4-40b8-9829-3840020aadf2\"}}";  
  
		// The io_service is required for all I/O  
		boost::asio::io_service ios;  
  
		// These objects perform our I/O  
		tcp::resolver resolver{ ios };  
		tcp::socket socket{ ios };  
  
		// Look up the domain name  
		auto const lookup = resolver.resolve({ host, port });  
  
		boost::asio::connect(socket, lookup);  
  
  
		http::request<http::string_body> req{ http::verb::get, target+paramOn, 11 };  
		req.set(http::field::host, host);  
		req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
		http::write(socket, req);  
		boost::beast::flat_buffer buffer;  
		http::response<http::dynamic_body> res;  
  
		http::read(socket, buffer, res);  
  
		// Write the message to standard out  
		std::cout << res << std::endl;  
  
		// Gracefully close the socket  
		boost::system::error_code ec;  
		socket.shutdown(tcp::socket::shutdown_both, ec);  
  
		if (ec && ec != boost::system::errc::not_connected)  
			throw boost::system::system_error{ ec };  
  
	}  
	catch (std::exception const& e)  
	{  
		std::cerr << "Error: " << e.what() << std::endl;  
		return EXIT_FAILURE;  
	}  
	return EXIT_SUCCESS;  
}`  
  
The server's encoding is chunked  
  
My solution is:  
  
`#ifndef Http_h__  
#define Http_h__  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <functional>  
#include <iostream>  
#include <string.h>  
#include <functional>  
  
using tcp = boost::asio::ip::tcp;  
namespace http = boost::beast::http;  
  
void fail(boost::system::error_code ec, char const* what);  
  
class CHttp :public std::enable_shared_from_this<CHttp> {  
  
	tcp::resolver resolver_;  
	tcp::socket socket_;  
	boost::beast::flat_buffer buffer_;  
	http::request<http::empty_body> req_;  
	http::response<http::string_body> res_;  
	http::response_parser<http::string_body> parser_;  
	std::function<int(std::uint64_t remain, boost::string_view body, boost::system::error_code ec)> callback_;  
  
public:  
	explicit  
		CHttp(boost::asio::io_service& ios)  
		:resolver_(ios)  
		, socket_(ios)   
	{  
	}  
	   
	void run(  
		std::string host,  
		std::string port,  
		std::string target,  
		std::function<int(std::uint64_t remain, boost::string_view body, boost::system::error_code ec)>callback);  
  
	void on_resolve(  
		boost::system::error_code ec,  
		tcp::resolver::iterator result);  
  
	void on_connect(  
		boost::system::error_code ec);  
  
	void on_write(  
		boost::system::error_code ec,  
		std::size_t bytes_transferred);  
  
	void on_read(  
		boost::system::error_code ec,  
		std::size_t byte_transferred);  
  
};  
  
  
#endif // Http_h__  
`  
  
`#include "Http.h"  
  
	void fail(boost::system::error_code ec, char const* what) {  
		std::cerr << what << ": " << ec.message() << "\n";  
	}  
  
	void CHttp::run(  
		std::string host,  
		std::string port,  
		std::string target,  
		std::function<int(std::uint64_t remain, boost::string_view body, boost::system::error_code ec)>callback) {  
		callback_ = callback;  
  
		req_.version(11);  
		req_.method(http::verb::get);  
		req_.target(target);  
		req_.set(http::field::host, host);  
		req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
		resolver_.async_resolve({ host, port },  
			std::bind(  
				&CHttp::on_resolve,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
	}  
  
	void CHttp::on_resolve(  
		boost::system::error_code ec,  
		tcp::resolver::iterator result) {  
		if (ec)  
			return fail(ec, "resolve");  
		boost::asio::async_connect(socket_, result,  
			std::bind(  
				&CHttp::on_connect,  
				shared_from_this(),  
				std::placeholders::_1));  
	}  
  
	void CHttp::on_connect(  
		boost::system::error_code ec) {  
		if (ec)  
			return fail(ec, "connect");  
		http::async_write(socket_, req_,  
			std::bind(  
				&CHttp::on_write,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
	}  
  
	void CHttp::on_write(  
		boost::system::error_code ec,  
		std::size_t bytes_transferred) {  
		boost::ignore_unused(bytes_transferred);  
		if (ec)  
			return fail(ec, "write");		  
  
		parser_.on_chunk_body(callback_);  
  
		http::async_read(socket_, buffer_, parser_,  
			std::bind(  
				&CHttp::on_read,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
	}  
  
	void CHttp::on_read(  
		boost::system::error_code ec,  
		std::size_t byte_transferred) {  
		boost::ignore_unused(byte_transferred);  
		if (ec)  
			return fail(ec, "read");  
		socket_.shutdown(tcp::socket::shutdown_both, ec);  
		if (ec && ec != boost::system::errc::not_connected)  
			return fail(ec, "shutdown");  
	}  
`  
  
Just use the on_chunk_body callback to get the body, I think this is a stupid way to get the just content.

---

## Comment 1

> Username: aikuimail  
> Created at: 2017-10-23 02:02:26 UTC  
> Url: https://github.com/boostorg/beast/issues/826#issuecomment-338528919  

Okay, I have solved this problem by google  
Use http::string_body instead of http::dynamic_body and then get res.body

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-23 03:19:17 UTC  
> Url: https://github.com/boostorg/beast/issues/826#issuecomment-338537794  

If you're going to quote code, consider using triple backquotes, as all of the formatting is lost in your example code.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-24 16:57:56 UTC  
> Url: https://github.com/boostorg/beast/issues/826#issuecomment-339059530  

Has this issue been resolved?

---

## Comment 4

> Username: lls2wow  
> Created at: 2017-11-16 23:09:44 UTC  
> Url: https://github.com/boostorg/beast/issues/826#issuecomment-345092761  

@aikuimail,  You  could use dynamic_body also  .  
  
Here is my code and it works well . I get the body use beast::buffers around it.  
	beast::flat_buffer b;  
	http::response<http::dynamic_body> res;  
	http::read(stream, b, res, ec); if (ec)  fail("read", ec);  
	stringstream ss;  
	ss << beast::buffers(res.body.data());

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-11-18 20:55:17 UTC  
> Url: https://github.com/boostorg/beast/issues/826#issuecomment-345470539  

It looks like the original issue has been resolved, so I'll go ahead and close this. Feel free to open a new issue if another problem arises, thanks!
