# #388 - Incorrect HTTP answer on multi threading io service run [Closed]

> Username: durs  
> Created at: 2017-05-25 09:02:35 UTC  
> Updated at: 2017-05-29 21:45:39 UTC  
> Closed at: 2017-05-29 08:58:59 UTC  
> Url: https://github.com/boostorg/beast/issues/388  

When io_service started on more then 1 thread, the answer comes to the old request.  
Using CommView, I have saw, that sometimes Beast sent duplicate request and server responds twice.  
This second response used with next request.  
For example (vc++14, boost 1.64, beast 43)  
  
```c++  
#include <memory>  
#include <string>  
#include <iostream>  
#include <boost/asio.hpp>  
#include <boost/bind.hpp>  
#include <boost/function.hpp>  
#include <beast/core.hpp>  
#include <beast/http.hpp>  
  
const int echo_thread_cnt = 2;  
const int echo_test_cnt = 10;  
const std::string echo_host = "urlecho.appspot.com";  
const std::string echo_target("/echo?status=200&Content-Type=text%2Fhtml&body=");  
int echo_test_num = 0;  
  
void echo_send(boost::asio::ip::tcp::socket *sock) {  
  
	// Prepare request target  
	int test_num = ++echo_test_num;  
	std::stringstream target;  
	target << echo_target;  
	for (int i = 0; i < echo_test_cnt; i++)  
		target << test_num << "%20";  
  
	// Prepare request  
	auto req = std::make_shared<beast::http::request<beast::http::string_body>>();  
	req->version = 11;  
	req->method("GET");  
	req->target(target.str());  
	req->fields.replace("Host", echo_host);  
	beast::http::prepare(*req);  
  
	// Write to log  
	std::cout << "sending " << test_num << std::endl;  
  
	// Send request  
	beast::http::async_write(*sock, *req, [sock, req, test_num](const beast::error_code &err) {  
  
		// Write result to log  
		std::cout << "sended " << test_num;  
		if (err) std::cout << " with error " << err;  
		std::cout << std::endl;  
  
		// On error try next  
		if (err) {  
			sock->get_io_service().post(boost::bind(&echo_send, sock));  
			return;  
		}  
  
		// Read answer  
		auto buf = std::make_shared<beast::flat_buffer>();  
		auto resp = std::make_shared<beast::http::response<beast::http::dynamic_body>>();  
		beast::http::async_read(*sock, *buf, *resp, [sock, resp, buf, test_num](const beast::error_code& err) {  
  
			// Write answer to log  
			std::cout << "answer " << test_num;  
			if (err) std::cout << " with error " << err;  
			std::cout << ": ";  
			  
			// Lopp by buffers and test answer  
			int invalid_answer = 0;  
			auto data = resp->body.data();  
			for (auto const& b : data) {  
				char const *b_ptr = boost::asio::buffer_cast<char const*>(b);  
				size_t b_size = boost::asio::buffer_size(b);  
				if (b_size > 0) {  
					if (!invalid_answer) invalid_answer = (atoi(b_ptr) != test_num) ? 1 : -1;  
					std::cout.write(b_ptr, b_size);  
				}  
			}  
			std::cout << std::endl;  
			  
			// Write error to log  
			if (invalid_answer > 0) std::cout << "!!! Invalid answer" << std::endl;  
  
			// Process next request  
			sock->get_io_service().post(boost::bind(&echo_send, sock));  
		});  
  
	});  
}  
  
int _tmain(int argc, _TCHAR* argv[])  
{  
	// Prepare connection  
	boost::asio::io_service ios;  
	boost::asio::ip::tcp::resolver echo_r{ ios };  
	boost::asio::ip::tcp::socket echo_sock{ ios };  
	boost::asio::connect(echo_sock, echo_r.resolve(boost::asio::ip::tcp::resolver::query{ echo_host, "http" }));  
  
	// Start test  
	echo_send(&echo_sock);  
  
	// Start additional io service threads  
	for (int n = 1; n < echo_thread_cnt; n++) {  
		new std::thread([&ios]() {  
			ios.run();  
		});  
	}  
	ios.run();  
	return 0;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-25 13:43:47 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304013287  

>`beast::http::async_write(*sock, *req, [sock, req, test_num](const beast::error_code &err) {...}`  
  
You need to wrap your completion handlers in a `boost::asio::io_service::strand`  
http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/overview/core/strands.html

---

## Comment 2

> Username: durs  
> Created at: 2017-05-26 09:48:05 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304240080  

Thanks, the test sample is working fine .. but my working application still produces errors, I will think further

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-26 15:49:51 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304318172  

Does your application function correctly when there is only one thread calling `io_service::run`?

---

## Comment 4

> Username: durs  
> Created at: 2017-05-27 07:51:48 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304435977  

Only one thread is used, successively several HTTP requests are sent through one connection. But periodically Beast sends a second request and the response does not match the request. Why this happens so far I can not understand.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-05-27 14:06:41 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304453496  

But you are setting `const int echo_thread_cnt = 2;` which means two threads?

---

## Comment 6

> Username: durs  
> Created at: 2017-05-27 15:25:01 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304458741  

This is only for a test with similar problems.  
But with io_service::strand works fine.

---

## Comment 7

> Username: durs  
> Created at: 2017-05-29 08:58:59 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304610619  

I solved my problem by replacing beast::http::async_write with boost::asio::async_write

---

## Comment 8

> Username: durs  
> Created at: 2017-05-29 09:29:04 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304617015  

And the above test works fine without boost::asio::strand.  
What is hidden behind the function beast::http::async_write?

---

## Comment 9

> Username: djarek  
> Created at: 2017-05-29 20:57:14 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304729440  

@durs   
If you don't use a strand neither the asio async_write nor beast's provide any guarantees about the result.   
Based on the snippet you provided I can infer that you're running on Windows.  
The asio version works most likely by pure coincidence, thanks to Windows' IOCP based implementation of asio's proactor. It is likely that if you ran your version with asio::async_write on Linux/BSD it wouldn't work. Also, there's no guarantee (even at the OS level) that you won't get interleaved data if you perform asynchronous writes to a socket. You should really either use an explicit strand(asio::strand) or an implicit one (HTTP 1/1 used as a half-duplex protocol, ordering of operations is guaranteed by the ordering of callbacks).  
  
As a side note:  
Your snippet leaks threads if io_service::run() exits ;).

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-05-29 21:45:39 UTC  
> Url: https://github.com/boostorg/beast/issues/388#issuecomment-304735060  

I'm confused about how `boost::asio::async_write` is in any way a replacement for `beast::http::async_write`. One of them takes a **ConstBufferSequence** while the other takes a `beast::http::message`. How are they interoperable?
