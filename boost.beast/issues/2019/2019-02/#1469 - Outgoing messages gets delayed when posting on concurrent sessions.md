# #1469 - Outgoing messages gets delayed when posting on concurrent sessions [Closed]

> Username: mlangberg  
> Created at: 2019-02-21 22:10:05 UTC  
> Updated at: 2019-02-21 23:07:17 UTC  
> Closed at: 2019-02-21 23:07:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1469  

I have a question. I am trying to do concurrent POSTs.  
  
In the example attached (based on http_client_async.cpp) I am creating 32 sessions.  
Each session posts a simple json.  
  
I measure time between send and on_read. Here is the result:  
  
when ALL posts are:  
  
{  
    "cnt": 1  
}  
  
1 on read after 1.4597 seconds.  
1 on read after 1.45425 seconds.  
1 on read after 1.46729 seconds.  
1 on read after 1.46489 seconds.  
1 on read after 1.47289 seconds.  
1 on read after 1.46404 seconds.  
1 on read after 1.46656 seconds.  
1 on read after 1.48164 seconds.  
1 on read after 1.47337 seconds.  
1 on read after 1.47995 seconds.  
1 on read after 1.48116 seconds.  
1 on read after 1.48786 seconds.  
1 on read after 1.48782 seconds.  
1 on read after 1.48803 seconds.  
1 on read after 1.49268 seconds.  
1 on read after 1.48539 seconds.  
1 on read after 1.49207 seconds.  
1 on read after 1.49808 seconds.  
1 on read after 1.48608 seconds.  
1 on read after 1.49574 seconds.  
1 on read after 1.49207 seconds.  
1 on read after 1.49172 seconds.  
1 on read after 1.48224 seconds.  
1 on read after 1.45465 seconds.  
1 on read after 1.44104 seconds.  
1 on read after 1.50292 seconds.  
1 on read after 1.49491 seconds.  
1 on read after 1.47988 seconds.  
1 on read after 1.50593 seconds.  
1 on read after 1.50729 seconds.  
1 on read after 1.51234 seconds.  
1 on read after 1.51344 seconds.  
  
  
If I modify the code so each post has a unique cnt value:  
  
(main) 	  
        // Launch the asynchronous operation  
	for (int i = 0; i < 32; i++) {  
		std::make_shared<session>(ioc)->run(**i**, host, port, target, version);  
	}  
  
Then I get this result:  
  
11 on read after 6.04243 seconds.  
3 on read after 8.48873 seconds.  
9 on read after 8.49988 seconds.  
6 on read after 8.50691 seconds.  
0 on read after 8.51251 seconds.  
7 on read after 8.51096 seconds.  
18 on read after 8.50881 seconds.  
17 on read after 8.51385 seconds.  
10 on read after 8.5214 seconds.  
16 on read after 8.52266 seconds.  
2 on read after 8.53341 seconds.  
12 on read after 8.53522 seconds.  
19 on read after 8.53321 seconds.  
5 on read after 8.54614 seconds.  
4 on read after 8.54839 seconds.  
14 on read after 8.55214 seconds.  
8 on read after 8.56129 seconds.  
13 on read after 8.56303 seconds.  
15 on read after 8.98278 seconds.  
1 on read after 8.99355 seconds.  
31 on read after 12.8651 seconds.  
30 on read after 12.9738 seconds.  
29 on read after 12.988 seconds.  
28 on read after 13.006 seconds.  
27 on read after 13.0216 seconds.  
26 on read after 13.0337 seconds.  
25 on read after 13.0406 seconds.  
24 on read after 13.0442 seconds.  
23 on read after 13.0495 seconds.  
22 on read after 13.0516 seconds.  
21 on read after 13.0555 seconds.  
20 on read after 13.0621 seconds.  
  
  
Why is it taken so much longer??  
  
  
  
  
When reporting a bug please include the following:  
### Version of Beast  
189  
  
### Steps necessary to reproduce the problem  
  
I have modified the http_client_async.cpp slightly  
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
// Example: HTTP client, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
  
#include <boost/property_tree/json_parser.hpp>  
#include <boost/property_tree/ptree.hpp>  
  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
using namespace std::chrono;  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(boost::system::error_code ec, char const* what)  
{  
	std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Performs an HTTP GET and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
	tcp::resolver resolver_;  
	tcp::socket socket_;  
	boost::beast::flat_buffer buffer_; // (Must persist between reads)  
	http::request<http::string_body> req_;  
	http::response<http::string_body> res_;  
	steady_clock::time_point tsend;  
  
public:  
	// Resolver and socket require an io_context  
	explicit  
		session(boost::asio::io_context& ioc)  
		: resolver_(ioc)  
		, socket_(ioc)  
	{  
	}  
  
	// Start the asynchronous operation  
	void  
		run(  
			int cnt,  
			char const* host,  
			char const* port,  
			char const* target,  
			int version)  
	{  
		// Set up an HTTP GET request message  
		req_.version(version);  
		req_.method(http::verb::post);  
		req_.target(target);  
		req_.set(http::field::host, host);  
		req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
		boost::property_tree::ptree ptree;  
  
		ptree.put("cnt", cnt);  
  
		std::stringstream ss;  
		write_json(ss, ptree);  
  
		req_.body() = ss.str();  
		req_.prepare_payload();  
  
		// Look up the domain name  
		resolver_.async_resolve(  
			host,  
			port,  
			std::bind(  
				&session::on_resolve,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
	}  
  
	void  
		on_resolve(  
			boost::system::error_code ec,  
			tcp::resolver::results_type results)  
	{  
		if (ec)  
			return fail(ec, "resolve");  
  
		// Make the connection on the IP address we get from a lookup  
		boost::asio::async_connect(  
			socket_,  
			results.begin(),  
			results.end(),  
			std::bind(  
				&session::on_connect,  
				shared_from_this(),  
				std::placeholders::_1));  
	}  
  
	void  
		on_connect(boost::system::error_code ec)  
	{  
		if (ec)  
			return fail(ec, "connect");  
  
		tsend = steady_clock::now();  
  
		// Send the HTTP request to the remote host  
		http::async_write(socket_, req_,  
			std::bind(  
				&session::on_write,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
	}  
  
	void  
		on_write(  
			boost::system::error_code ec,  
			std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		if (ec)  
			return fail(ec, "write");  
  
		// Receive the HTTP response  
		http::async_read(socket_, buffer_, res_,  
			std::bind(  
				&session::on_read,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
	}  
  
	void  
		on_read(  
			boost::system::error_code ec,  
			std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		if (ec)  
			return fail(ec, "read");  
  
		// Write the message to standard out  
		//std::cout << res_.body() << std::endl;  
  
		boost::property_tree::ptree ptree;  
		try {  
			std::stringstream ss(res_.body());  
			read_json(ss, ptree);  
		}  
		catch (std::exception e) {  
			std::cout << e.what() << std::endl;  
		}  
  
		steady_clock::time_point tnow = steady_clock::now();  
		duration<double> time_span = duration_cast<duration<double>>(tnow - tsend);  
		std::cout << ptree.get<std::string>("json.cnt", "") << " on read after " << time_span.count() << " seconds." << std::endl;  
  
		  
		  
		  
  
		// Gracefully close the socket  
		socket_.shutdown(tcp::socket::shutdown_both, ec);  
  
		// not_connected happens sometimes so don't bother reporting it.  
		if (ec && ec != boost::system::errc::not_connected)  
			return fail(ec, "shutdown");  
  
		// If we get here then the connection is closed gracefully  
	}  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char** argv)  
{  
	  
	auto const host = "httpbin.org";  
	auto const port = "80";  
	auto const target = "/post";  
	int version = 10;  
  
	// The io_context is required for all I/O  
	boost::asio::io_context ioc;  
  
	// Launch the asynchronous operation  
	for (int i = 0; i < 32; i++) {  
		std::make_shared<session>(ioc)->run(1, host, port, target, version);  
	}  
  
	// Run the I/O service. The call will return when  
	// the get operation is complete.  
	ioc.run();  
  
	return EXIT_SUCCESS;  
}  
  
  
### All relevant compiler information  
  
I am compiling and running in Visual Studio 2017.  
With boost 1.69

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-21 22:14:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1469#issuecomment-466188719  

The only difference I can see is that you are putting a different number in some JSON:  
```  
ptree.put("cnt", cnt);  
```  
What server are you connecting to? What does the server do with this JSON? Are you running DEBUG or RELEASE?

---

## Comment 2

> Username: mlangberg  
> Created at: 2019-02-21 22:39:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1469#issuecomment-466196926  

Hi,  
  
Thanks for getting back to me.  
  
Yes you are right, the only difference is that:  
  
1. I send out the same POST body on all sessions vs  
2. POSTING different bodies (cnt incremented for each session).  
  
I don't understand why #2 would take much longer time,...  
  
The program just wait for a long time before sending out any messages,. I  
have checked with wire shark.  
  
  
I am connecting to httpbin.org just for recreating the problem.  
  
I get same behavior for both DEBUG and RELEASE.  
  
  
Thanks  
Mike  
  
On Thu, Feb 21, 2019 at 2:14 PM Vinnie Falco <notifications@github.com>  
wrote:  
  
> The only difference I can see is that you are putting a different number  
> in some JSON:  
>  
> ptree.put("cnt", cnt);  
>  
> What server are you connecting to? What does the server do with this JSON?  
> Are you running DEBUG or RELEASE?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1469#issuecomment-466188719>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AENw8Q5HcVoY6SKxX4yo4dW2_Dgh5Ej0ks5vPxpTgaJpZM4bIbfa>  
> .  
>

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-21 22:57:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1469#issuecomment-466202428  

I have no idea, but it doesn't look like a problem with Beast. Try not using property tree and build the payload yourself manually.

---

## Comment 4

> Username: mlangberg  
> Created at: 2019-02-21 23:05:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1469#issuecomment-466204917  

Hi,  
  
You are right. I tested the program on another PC and it worked without any  
problem.  
  
The problem seem to be with my PC.  
  
I just wasted several hours debugging the wrong thing.  
  
Sorry for bothering you.  
  
/Mike  
  
On Thu, Feb 21, 2019 at 2:57 PM Vinnie Falco <notifications@github.com>  
wrote:  
  
> I have no idea, but it doesn't look like a problem with Beast. Try not  
> using property tree and build the payload yourself manually.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1469#issuecomment-466202428>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AENw8eFDkMKIvTb3RjxZ85DAdIKUzVPcks5vPyRagaJpZM4bIbfa>  
> .  
>

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-02-21 23:07:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1469#issuecomment-466205383  

> Sorry for bothering you.  
  
I'm glad to help, and I am glad to see you got it sorted!
