# #2324 - Beast performance issues [Closed]

> Username: kqbi  
> Created at: 2021-10-08 00:12:33 UTC  
> Updated at: 2021-10-11 01:24:38 UTC  
> Closed at: 2021-10-11 01:24:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2324  

in linux, Use http_ client_ Async.cpp links Minio to download 5MB files, which takes 80+ MS, but using curl to download files of the same size only takes 50+ Ms. beast so slow，why？

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-08 00:13:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2324#issuecomment-938241509  

And how do you think I would be able to figure it out from one sentence without additional details?

---

## Comment 2

> Username: kqbi  
> Created at: 2021-10-08 00:45:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2324#issuecomment-938252642  

I think you can use http_ client_ async.cpp and curl download 5MB files of the same size for comparison

---

## Comment 3

> Username: kqbi  
> Created at: 2021-10-08 01:55:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2324#issuecomment-938276500  

Using nginx to build a static file server.  use curl   
```  
curl -r 0- -o test.mp4 http://172.31.223.87:8900/test.mp4  
```  
you can compare with http_ client_ async.cpp

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-10-08 05:09:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2324#issuecomment-938348300  

Would you mind including the headers emitted by both your beast program and curl

---

## Comment 5

> Username: kqbi  
> Created at: 2021-10-08 06:32:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2324#issuecomment-938382405  

use boost 1.77, http_client_async.cpp:   
```  
//  
// Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)  
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
#include <boost/asio/strand.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Performs an HTTP GET and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
    tcp::resolver resolver_;  
    beast::tcp_stream stream_;  
    beast::flat_buffer buffer_; // (Must persist between reads)  
    http::request<http::empty_body> req_;  
    http::response<http::string_body> res_;  
  
public:  
    // Objects are constructed with a strand to  
    // ensure that handlers do not execute concurrently.  
    explicit  
    session(net::io_context& ioc)  
        : resolver_(net::make_strand(ioc))  
        , stream_(net::make_strand(ioc))  
    {  
    }  
  
    // Start the asynchronous operation  
    void  
    run(  
        char const* host,  
        char const* port,  
        char const* target,  
        int version)  
    {  
        // Set up an HTTP GET request message  
        req_.version(version);  
        req_.method(http::verb::get);  
        req_.target(target);  
        req_.set(http::field::host, host);  
        req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
        req_.set(http::field::range, "0-");  
        // Look up the domain name  
        resolver_.async_resolve(  
            host,  
            port,  
            beast::bind_front_handler(  
                &session::on_resolve,  
                shared_from_this()));  
    }  
  
    void  
    on_resolve(  
        beast::error_code ec,  
        tcp::resolver::results_type results)  
    {  
        if(ec)  
            return fail(ec, "resolve");  
  
        // Set a timeout on the operation  
        stream_.expires_after(std::chrono::seconds(30));  
  
        // Make the connection on the IP address we get from a lookup  
        stream_.async_connect(  
            results,  
            beast::bind_front_handler(  
                &session::on_connect,  
                shared_from_this()));  
    }  
  
    void  
    on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type)  
    {  
        if(ec)  
            return fail(ec, "connect");  
  
		int bufSize = 4 * 1024 * 1024;  
		boost::asio::socket_base::send_buffer_size sendBuf(bufSize);  
		stream_.socket().set_option(sendBuf);  
  
		boost::asio::socket_base::receive_buffer_size recvBuf(bufSize);  
		stream_.socket().set_option(recvBuf);  
  
		boost::asio::ip::tcp::no_delay noDelay( true );  
		stream_.socket().set_option(noDelay);  
  
		// Set a timeout on the operation  
        stream_.expires_after(std::chrono::seconds(30));  
  
        // Send the HTTP request to the remote host  
        http::async_write(stream_, req_,  
            beast::bind_front_handler(  
                &session::on_write,  
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
          
        // Receive the HTTP response  
        http::async_read(stream_, buffer_, res_,  
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
  
        if(ec)  
            return fail(ec, "read");  
  
        // Write the message to standard out  
        //std::cout << res_ << std::endl;  
  
        // Gracefully close the socket  
        stream_.socket().shutdown(tcp::socket::shutdown_both, ec);  
  
        // not_connected happens sometimes so don't bother reporting it.  
        if(ec && ec != beast::errc::not_connected)  
            return fail(ec, "shutdown");  
  
        // If we get here then the connection is closed gracefully  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char** argv)  
{  
    // Check command line arguments.  
     
    auto const host = "172.31.223.87";  
    auto const port = "8900";  
    auto const target = "/test.mp4";  
    int version = 11;  
  
    // The io_context is required for all I/O  
    net::io_context ioc;  
boost::asio::executor_work_guard<boost::asio::io_context::executor_type> work(  
             boost::asio::make_work_guard(ioc));  
  
	std::thread t([&ioc](){ioc.run();});  
    // Launch the asynchronous operation  
    std::make_shared<session>(ioc)->run(host, port, target, version);  
  
    // Run the I/O service. The call will return when  
	// the get operation is complete.  
#if _WIN32  
	Sleep(3000);  
#else   
	sleep(3);  
#endif  
	ioc.stop();  
	t.join();  
	return EXIT_SUCCESS;  
}  
  
```  
use curl in shell:  
```  
curl -r 0- -o test.mp4 http://172.31.223.87:8900/test.mp4  
```  
![1633674748768](https://user-images.githubusercontent.com/40823194/136509296-e9102de0-9ff6-48aa-b378-f23de034151f.png)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-10-09 04:09:11 UTC  
> Updated at: 2021-10-09 04:09:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2324#issuecomment-939221409  

curl is not asynchronous so you are going to see slightly better results with it in a single-operation test. try the synchronous Beast client.

---

## Comment 7

> Username: kqbi  
> Created at: 2021-10-11 01:24:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2324#issuecomment-939601085  

OK.synchronous Beast client is faster !!! Thank you for your clarification.
