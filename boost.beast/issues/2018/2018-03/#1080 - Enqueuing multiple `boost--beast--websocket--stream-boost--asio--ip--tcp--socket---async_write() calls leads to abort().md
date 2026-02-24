# #1080 - Enqueuing multiple `boost::beast::websocket::stream<boost::asio::ip::tcp::socket>::async_write() calls leads to abort() [Closed]

> Username: fake-name  
> Created at: 2018-03-26 22:07:30 UTC  
> Updated at: 2018-09-29 13:22:51 UTC  
> Closed at: 2018-03-27 00:20:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1080  

### Version of Beast  
  
    #define BOOST_BEAST_VERSION 144  
  
(Boost overall version 1.66.0)  
  
### Steps necessary to reproduce the problem  
  
Basically, I have a project that involves a websocket interface that has both a message->response interface, and unprompted messages to the client from the server.  
  
My understanding about ASIO is that I can just call `async_write()` bound to the correct strand, and the resulting writes will be enqueued and then send.  
  
However, it seems like trying to queue up multiple writes on the websocket leads to a debug abort:  
`Assertion failed: ! base_, file c:\boost\boost_1_66_0\boost\beast\websocket\detail\pausation.hpp, line 213`  
  
I've built a small test out of the `advanced_server.cpp` file. The relevant bits:  
  
```  
void  
on_read(  
    boost::system::error_code ec,  
    std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	// Happens when the timer closes the socket  
	if (ec == boost::asio::error::operation_aborted)  
	{  
		return;  
	}  
  
	// This indicates that the websocket_session was closed  
	if (ec == websocket::error::closed)  
	{  
		return;  
	}  
  
	if (ec)  
	{  
		fail(ec, "read");  
	}  
	static int resp_counter = 0;  
	resp_counter += 1;  
  
	std::string msg = boost::beast::buffers_to_string(this->buffer_.data());  
  
	// Clear the buffer  
	buffer_.consume(buffer_.size());  
  
	std::string resp_1 = msg + " - resp " + std::to_string(resp_counter) + ".";  
	auto outgoing_buf_1 = std::make_shared<std::vector<unsigned char> >(resp_1.begin(), resp_1.end());  
	// Echo the message  
	ws_.text(ws_.got_text());  
  
	ws_.async_write(  
		boost::asio::buffer(*(outgoing_buf_1.get())),  
		boost::asio::bind_executor(  
			strand_,  
			std::bind(  
				&websocket_session::on_write,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2,  
				outgoing_buf_1,  
				resp_counter,  
				true  
			)  
		)  
	);  
  
	resp_counter += 1;  
  
	std::string resp_2 = msg + " - resp " + std::to_string(resp_counter) + ".";  
	auto outgoing_buf_2 = std::make_shared<std::vector<unsigned char> >(resp_2.begin(), resp_2.end());  
  
	ws_.async_write(  
		boost::asio::buffer(*(outgoing_buf_2.get())),  
		boost::asio::bind_executor(  
			strand_,  
			std::bind(  
				&websocket_session::on_write,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2,  
				outgoing_buf_2,  
				resp_counter,  
				false  
			)  
		)  
	);  
}  
```  
```  
void  
on_write(  
    boost::system::error_code ec,  
    std::size_t bytes_transferred,  
	std::shared_ptr<std::vector<unsigned char> > resp_ptr,   
	int resp_counter,  
	bool listen)  
{  
	boost::ignore_unused(bytes_transferred);  
	boost::ignore_unused(resp_ptr);  
	std::string msg_str(resp_ptr->begin(), resp_ptr->end());  
  
	std::cout << "wrote " << std::to_string(resp_counter) << ": '" << msg_str << "'." << std::endl;  
	// Happens when the timer closes the socket  
	if (ec == boost::asio::error::operation_aborted)  
	{  
		return;  
	}  
  
	if (ec)  
	{  
		return fail(ec, "write");  
	}  
  
	// Do another read  
	if (listen)  
		do_read();  
}  
```  
  
My understanding is since the writes are explicitly bound to a strand, they should be serialized, and therefore you can issue multiple writes at the same time. Perhaps this is not the correct understanding?  
  
### All relevant compiler information  
  
MSVC++ 2015  
  
  
Complete source:  
  
```  
//  
// Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
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
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <boost/config.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace http = boost::beast::http;            // from <boost/beast/http.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
// Return a reasonable mime type based on the extension of a file.  
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
  
// Append an HTTP rel-path to a local filesystem path.  
// The returned path is normalized for the platform.  
std::string  
path_cat(  
    boost::beast::string_view base,  
    boost::beast::string_view path)  
{  
	if (base.empty())  
	{  
		return path.to_string();  
	}  
	std::string result = base.to_string();  
	#if BOOST_MSVC  
	char constexpr path_separator = '\\';  
	if (result.back() == path_separator)  
	{  
		result.resize(result.size() - 1);  
	}  
	result.append(path.data(), path.size());  
	for (auto& c : result)  
		if (c == '/')  
		{  
			c = path_separator;  
		}  
	#else  
	char constexpr path_separator = '/';  
	if (result.back() == path_separator)  
	{  
		result.resize(result.size() - 1);  
	}  
	result.append(path.data(), path.size());  
	#endif  
	return result;  
}  
  
// This function produces an HTTP response for the given  
// request. The type of the response object depends on the  
// contents of the request, so the interface requires the  
// caller to pass a generic lambda for receiving the response.  
template <  
    class Body, class Allocator,  
    class Send >  
void  
handle_request(  
    boost::beast::string_view doc_root,  
    http::request<Body, http::basic_fields<Allocator>>&& req,  
    Send&& send)  
{  
	// Returns a bad request response  
	auto const bad_request =  
	    [&req](boost::beast::string_view why)  
	{  
		http::response<http::string_body> res{http::status::bad_request, req.version()};  
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
		http::response<http::string_body> res{http::status::not_found, req.version()};  
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
		http::response<http::string_body> res{http::status::internal_server_error, req.version()};  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "An error occurred: '" + what.to_string() + "'";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Make sure we can handle the method  
	if ( req.method() != http::verb::get &&  
	        req.method() != http::verb::head)  
	{  
		return send(bad_request("Unknown HTTP-method"));  
	}  
  
	// Request path must be absolute and not contain "..".  
	if ( req.target().empty() ||  
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
		http::response<http::empty_body> res{http::status::ok, req.version()};  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to GET request  
	http::response<http::file_body> res  
	{  
		std::piecewise_construct,  
		std::make_tuple(std::move(body)),  
		std::make_tuple(http::status::ok, req.version())};  
	res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
	res.set(http::field::content_type, mime_type(path));  
	res.content_length(body.size());  
	res.keep_alive(req.keep_alive());  
	return send(std::move(res));  
}  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(boost::system::error_code ec, char const* what)  
{  
	std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class websocket_session : public std::enable_shared_from_this<websocket_session>  
{  
		websocket::stream<tcp::socket> ws_;  
		boost::asio::strand <  
		boost::asio::io_context::executor_type > strand_;  
		boost::asio::steady_timer timer_;  
		boost::beast::multi_buffer buffer_;  
  
	public:  
		// Take ownership of the socket  
		explicit  
		websocket_session(tcp::socket socket)  
			: ws_(std::move(socket))  
			, strand_(ws_.get_executor())  
			, timer_(ws_.get_executor().context(),  
			         (std::chrono::steady_clock::time_point::max)())  
		{  
		}  
  
		// Start the asynchronous operation  
		template<class Body, class Allocator>  
		void  
		run(http::request<Body, http::basic_fields<Allocator>> req)  
		{  
			// Run the timer. The timer is operated  
			// continuously, this simplifies the code.  
			on_timer({});  
  
			// Set the timer  
			timer_.expires_after(std::chrono::seconds(15));  
  
			// Accept the websocket handshake  
			ws_.async_accept(  
			    req,  
			    boost::asio::bind_executor(  
			        strand_,  
			        std::bind(  
			            &websocket_session::on_accept,  
			            shared_from_this(),  
			            std::placeholders::_1)));  
		}  
  
		// Called when the timer expires.  
		void  
		on_timer(boost::system::error_code ec)  
		{  
			if (ec && ec != boost::asio::error::operation_aborted)  
			{  
				return fail(ec, "timer");  
			}  
  
			// Verify that the timer really expired since the deadline may have moved.  
			if (timer_.expiry() <= std::chrono::steady_clock::now())  
			{  
				// Closing the socket cancels all outstanding operations. They  
				// will complete with boost::asio::error::operation_aborted  
				ws_.next_layer().shutdown(tcp::socket::shutdown_both, ec);  
				ws_.next_layer().close(ec);  
				return;  
			}  
  
			// Wait on the timer  
			timer_.async_wait(  
			    boost::asio::bind_executor(  
			        strand_,  
			        std::bind(  
			            &websocket_session::on_timer,  
			            shared_from_this(),  
			            std::placeholders::_1)));  
		}  
  
		void  
		on_accept(boost::system::error_code ec)  
		{  
			// Happens when the timer closes the socket  
			if (ec == boost::asio::error::operation_aborted)  
			{  
				return;  
			}  
  
			if (ec)  
			{  
				return fail(ec, "accept");  
			}  
  
			// Read a message  
			do_read();  
		}  
  
		void  
		do_read()  
		{  
			// Set the timer  
			timer_.expires_after(std::chrono::seconds(15));  
  
			// Read a message into our buffer  
			ws_.async_read(  
			    buffer_,  
			    boost::asio::bind_executor(  
			        strand_,  
			        std::bind(  
			            &websocket_session::on_read,  
			            shared_from_this(),  
			            std::placeholders::_1,  
			            std::placeholders::_2)));  
		}  
  
		void  
		on_read(  
		    boost::system::error_code ec,  
		    std::size_t bytes_transferred)  
		{  
			boost::ignore_unused(bytes_transferred);  
  
			// Happens when the timer closes the socket  
			if (ec == boost::asio::error::operation_aborted)  
			{  
				return;  
			}  
  
			// This indicates that the websocket_session was closed  
			if (ec == websocket::error::closed)  
			{  
				return;  
			}  
  
			if (ec)  
			{  
				fail(ec, "read");  
			}  
			static int resp_counter = 0;  
			resp_counter += 1;  
  
			std::string msg = boost::beast::buffers_to_string(this->buffer_.data());  
  
			// Clear the buffer  
			buffer_.consume(buffer_.size());  
  
			std::string resp_1 = msg + " - resp " + std::to_string(resp_counter) + ".";  
			auto outgoing_buf_1 = std::make_shared<std::vector<unsigned char> >(resp_1.begin(), resp_1.end());  
			// Echo the message  
			ws_.text(ws_.got_text());  
  
			ws_.async_write(  
				boost::asio::buffer(*(outgoing_buf_1.get())),  
				boost::asio::bind_executor(  
					strand_,  
					std::bind(  
						&websocket_session::on_write,  
						shared_from_this(),  
						std::placeholders::_1,  
						std::placeholders::_2,  
						outgoing_buf_1,  
						resp_counter,  
						true  
					)  
				)  
			);  
  
			resp_counter += 1;  
  
			std::string resp_2 = msg + " - resp " + std::to_string(resp_counter) + ".";  
			auto outgoing_buf_2 = std::make_shared<std::vector<unsigned char> >(resp_2.begin(), resp_2.end());  
  
			ws_.async_write(  
				boost::asio::buffer(*(outgoing_buf_2.get())),  
				boost::asio::bind_executor(  
					strand_,  
					std::bind(  
						&websocket_session::on_write,  
						shared_from_this(),  
						std::placeholders::_1,  
						std::placeholders::_2,  
						outgoing_buf_2,  
						resp_counter,  
						false  
					)  
				)  
			);  
		}  
  
		void  
		on_write(  
		    boost::system::error_code ec,  
		    std::size_t bytes_transferred,  
			std::shared_ptr<std::vector<unsigned char> > resp_ptr,   
			int resp_counter,  
			bool listen)  
		{  
			boost::ignore_unused(bytes_transferred);  
			boost::ignore_unused(resp_ptr);  
			std::string msg_str(resp_ptr->begin(), resp_ptr->end());  
  
			std::cout << "wrote " << std::to_string(resp_counter) << ": '" << msg_str << "'." << std::endl;  
			// Happens when the timer closes the socket  
			if (ec == boost::asio::error::operation_aborted)  
			{  
				return;  
			}  
  
			if (ec)  
			{  
				return fail(ec, "write");  
			}  
  
			// Do another read  
			if (listen)  
				do_read();  
		}  
};  
  
// Handles an HTTP server connection  
class http_session : public std::enable_shared_from_this<http_session>  
{  
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
					if (! items_.empty())  
					{  
						(*items_.front())();  
					}  
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
							    self_.socket_,  
							    msg_,  
							    boost::asio::bind_executor(  
							        self_.strand_,  
							        std::bind(  
							            &http_session::on_write,  
							            self_.shared_from_this(),  
							            std::placeholders::_1,  
							            msg_.need_eof())));  
						}  
					};  
  
					// Allocate and store the work  
					items_.emplace_back(new work_impl(self_, std::move(msg)));  
  
					// If there was no previous work, start this one  
					if (items_.size() == 1)  
					{  
						(*items_.front())();  
					}  
				}  
		};  
  
		tcp::socket socket_;  
		boost::asio::strand <  
		boost::asio::io_context::executor_type > strand_;  
		boost::asio::steady_timer timer_;  
		boost::beast::flat_buffer buffer_;  
		std::string const& doc_root_;  
		http::request<http::string_body> req_;  
		queue queue_;  
  
	public:  
		// Take ownership of the socket  
		explicit  
		http_session(  
		    tcp::socket socket,  
		    std::string const& doc_root)  
			: socket_(std::move(socket))  
			, strand_(socket_.get_executor())  
			, timer_(socket_.get_executor().context(),  
			         (std::chrono::steady_clock::time_point::max)())  
			, doc_root_(doc_root)  
			, queue_(*this)  
		{  
		}  
  
		// Start the asynchronous operation  
		void  
		run()  
		{  
			// Run the timer. The timer is operated  
			// continuously, this simplifies the code.  
			on_timer({});  
  
			do_read();  
		}  
  
		void  
		do_read()  
		{  
			// Set the timer  
			timer_.expires_after(std::chrono::seconds(15));  
  
			// Read a request  
			http::async_read(socket_, buffer_, req_,  
			                 boost::asio::bind_executor(  
			                     strand_,  
			                     std::bind(  
			                         &http_session::on_read,  
			                         shared_from_this(),  
			                         std::placeholders::_1)));  
		}  
  
		// Called when the timer expires.  
		void  
		on_timer(boost::system::error_code ec)  
		{  
			if (ec && ec != boost::asio::error::operation_aborted)  
			{  
				return fail(ec, "timer");  
			}  
  
			// Verify that the timer really expired since the deadline may have moved.  
			if (timer_.expiry() <= std::chrono::steady_clock::now())  
			{  
				// Closing the socket cancels all outstanding operations. They  
				// will complete with boost::asio::error::operation_aborted  
				socket_.shutdown(tcp::socket::shutdown_both, ec);  
				socket_.close(ec);  
				return;  
			}  
  
			// Wait on the timer  
			timer_.async_wait(  
			    boost::asio::bind_executor(  
			        strand_,  
			        std::bind(  
			            &http_session::on_timer,  
			            shared_from_this(),  
			            std::placeholders::_1)));  
		}  
  
		void  
		on_read(boost::system::error_code ec)  
		{  
			// Happens when the timer closes the socket  
			if (ec == boost::asio::error::operation_aborted)  
			{  
				return;  
			}  
  
			// This means they closed the connection  
			if (ec == http::error::end_of_stream)  
			{  
				return do_close();  
			}  
  
			if (ec)  
			{  
				return fail(ec, "read");  
			}  
  
			// See if it is a WebSocket Upgrade  
			if (websocket::is_upgrade(req_))  
			{  
				// Create a WebSocket websocket_session by transferring the socket  
				std::make_shared<websocket_session>(  
				    std::move(socket_))->run(std::move(req_));  
				return;  
			}  
  
			// Send the response  
			handle_request(doc_root_, std::move(req_), queue_);  
  
			// If we aren't at the queue limit, try to pipeline another request  
			if (! queue_.is_full())  
			{  
				do_read();  
			}  
		}  
  
		void  
		on_write(boost::system::error_code ec, bool close)  
		{  
			// Happens when the timer closes the socket  
			if (ec == boost::asio::error::operation_aborted)  
			{  
				return;  
			}  
  
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
  
			// Inform the queue that a write completed  
			if (queue_.on_write())  
			{  
				// Read another request  
				do_read();  
			}  
		}  
  
		void  
		do_close()  
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
			acceptor_.listen(  
			    boost::asio::socket_base::max_listen_connections, ec);  
			if (ec)  
			{  
				fail(ec, "listen");  
				return;  
			}  
		}  
  
		// Start accepting incoming connections  
		void  
		run()  
		{  
			if (! acceptor_.is_open())  
			{  
				return;  
			}  
			do_accept();  
		}  
  
		void  
		do_accept()  
		{  
			acceptor_.async_accept(  
			    socket_,  
			    std::bind(  
			        &listener::on_accept,  
			        shared_from_this(),  
			        std::placeholders::_1));  
		}  
  
		void  
		on_accept(boost::system::error_code ec)  
		{  
			if (ec)  
			{  
				fail(ec, "accept");  
			}  
			else  
			{  
				// Create the http_session and run it  
				std::make_shared<http_session>(  
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
  
	auto const address = boost::asio::ip::make_address("0.0.0.0");  
	auto const port = static_cast<unsigned short>(8080);  
	std::string const doc_root = "./";  
	auto const threads = 5;  
  
	// The io_context is required for all I/O  
	boost::asio::io_context ioc{threads};  
  
	// Create and launch a listening port  
	std::make_shared<listener>(  
	    ioc,  
	    tcp::endpoint{address, port},  
	    doc_root)->run();  
  
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
  
`index.html`  
  
```  
<!DOCTYPE html>  
<meta charset="utf-8" />  
<html>  
	<head>  
	<title>WebSocket Test</title>  
</head>  
<body>  
	<script language="javascript" type="text/javascript">  
  
	var wsUri = "ws://127.0.0.1:8080/";  
	var output;  
  
	function init()  
	{  
		output = document.getElementById("output");  
		testWebSocket();  
	}  
  
	function testWebSocket()  
	{  
		websocket = new WebSocket(wsUri);  
		websocket.onopen = function(evt) { onOpen(evt) };  
		websocket.onclose = function(evt) { onClose(evt) };  
		websocket.onmessage = function(evt) { onMessage(evt) };  
		websocket.onerror = function(evt) { onError(evt) };  
	}  
  
	var pre = document.createElement("p");  
	pre.style.wordWrap = "break-word";  
  
	function onOpen(evt)  
	{  
		output.appendChild(pre);  
  
		writeToScreen("CONNECTED");  
		doSend();  
	}  
  
	function onClose(evt)  
	{  
		writeToScreen("DISCONNECTED");  
	}  
  
	function onMessage(evt)  
	{  
		writeToScreen('<span style="color: blue;">RESPONSE: ' + evt.data+'</span>');  
		setTimeout(doSend, 500);  
	}  
  
	function onError(evt)  
	{  
		writeToScreen('<span style="color: red;">ERROR:</span> ' + evt.data);  
	}  
  
	function doSend()  
	{  
		writeToScreen("SENT: " + "Poke");  
		websocket.send("Poke");  
	}  
  
  
	function writeToScreen(message)  
	{  
		pre.innerHTML = pre.innerHTML + "<br>" + message;  
	}  
  
	window.addEventListener("load", init, false);  
  
	</script>  
  
	<h2>WebSocket Test</h2>  
  
	<div id="output"></div>  
</body>  
</html>  
```

---

## Comment 1

> Username: fake-name  
> Created at: 2018-03-26 22:13:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1080#issuecomment-376329198  

Ironically, I just noticed https://github.com/boostorg/beast/issues/1070, which seems to indicate that this *should* be safe?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-03-26 23:31:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1080#issuecomment-376345668  

>My understanding is since the writes are explicitly bound to a strand, they should be serialized, and therefore you can issue multiple writes at the same time. Perhaps this is not the correct understanding?  
  
Not correct. Beast does not performing queuing for you, that is the responsibility of the application. You can only have one call to `async_write` active at a time. The assertion detects that condition.  
  
>Ironically, I just noticed #1070, which seems to indicate that this should be safe?  
  
You can have simultaneous *different* operations. For example you can have `async_write`, `async_read`, and `async_ping` active at the same time. But you can't have two of the same operation.

---

## Comment 3

> Username: fake-name  
> Created at: 2018-03-26 23:44:56 UTC  
> Updated at: 2018-03-26 23:50:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1080#issuecomment-376347927  

Ah, ok, that clears that issue up. Thanks!  
  
Would it make sense to have beast to do the queing? I (*think*) plain ASIO does write queuing, wouldn't beast doing the same be consistent (or am I not understanding ASIO)?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-03-27 00:35:17 UTC  
> Updated at: 2018-03-27 00:36:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1080#issuecomment-376356590  

I don't think Asio does write queuing. If Beast did the queuing, it would raise all sorts of questions. How is memory allocated to store the queued messages? What policy is used to prevent unbounded growth? What technique is used to limit the amount of bandwidth used? A queue is no simple thing, and the Beast philosophy is to avoid making odd choices on behalf of the user. Since a queue can be built on top of Beast in an almost limitless number of different ways depending on the needs of the application, this behavior is left to users to define.  
  
Nothing stops people from building a higher level library on top of Beast that does offer queueing. And if it satisfies most people's needs, it would become popular.

---

## Comment 5

> Username: fake-name  
> Created at: 2018-03-27 00:57:07 UTC  
> Updated at: 2018-03-27 01:14:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1080#issuecomment-376360109  

Huh, doing some more reading, and apparently strands are  basically intended for *handlers*, so the issue was my misunderstanding.  
  
AFICT, what'll happen is the async_write_some calls for both writes will occur with some runtime-determined interleaving in the same strand. It's thread "safe", but it'd be completely broken.  
  
Anyways, I rewrote my system with chained callbacks and a queue, and it seems to be working fine, now.

---

## Comment 6

> Username: reddwarf69  
> Created at: 2018-09-29 05:55:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1080#issuecomment-425618685  

@vinniefalco `Nothing stops people from building a higher level library on top of Beast that does offer queueing. And if it satisfies most people's needs, it would become popular.`  
  
Have you though about doing it? boost::beast::helpers?  
  
I was thinking about this and it seems to me most people needing to queue writes are going to have a class with a `write(std::string message)` method and queue those strings. If the message is composed of two buffers they will copy them into the string and that's it. Not efficient, but easy, fuck scatter/gather I/O and ConstBufferSequence.  
  
It seems to me that most users would want to keep using ConstBufferSequence, the ConstBufferSequence passed to async_write is what needs to be queued. And there is basically only one way to implement it, no? Each ConstBufferSequence can be of a different type, you can't store them into a `vector<T>`, you can't merge them into a single Sequence, so you need to allocate memory for each one independently and there is only one way to allocate that memory: using the handler associated allocator (`How is memory allocated to store the queued messages?`). The whole thing would work similar to timers, with a write_queue in place of the timer_queue (https://github.com/chriskohlhoff/asio/blob/master/asio/include/asio/detail/timer_queue.hpp). Each element containing the ConstBufferSequence and the CompletionHandler. When looking at the next element in the queue, instead of setting the system timer facility, you start a write. Since you are not deciding the allocation you don't need to worry about being cache friendly or anything exotic, each write_queue element would link to each other making a linked list.  
  
`What policy is used to prevent unbounded growth?`  
Don't have a policy for this, let an unbounded growth. The user of this boost::beast::helpers::stream_with_queue knows how much data it has queued and how much data has already been writen to the socket buffer (the CompletionHandler has executed). It can still define his own policy.  
  
`What technique is used to limit the amount of bandwidth used?`  
Probably something like Linux's SO_MAX_PACING_RATE should be used. But even without this, the user can still limit the rate at which it writes into the queue of boost::beast::helpers::stream_with_queue. That's no different than limiting the rate at which you write to a socket (i.e. the socket buffer/queue), you are just appending your queue to the one of the socket. Most of the time the user doesn't even know the size of the socket buffer, so he doesn't mind if you make it bigger.  
  
  
Now, I may have got this completely wrong, it would not be the first time. But if there is really only one way to do the queue "right" (at least for 99% of the user cases), but it's difficult enough to implement to make everybody do it "wrong", it looks like offering the "right" solution would make the world a bit better.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-09-29 13:22:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1080#issuecomment-425644956  

> it looks like offering the "right" solution would make the world a bit better.  
  
Well, what you describe is entirely implementable as a separate class which works with a `websocket::stream`. You might consider writing it :)
