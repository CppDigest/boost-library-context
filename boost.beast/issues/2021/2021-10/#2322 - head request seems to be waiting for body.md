# #2322 - head request seems to be waiting for body [Open]

> Username: foosoftsrlold  
> Created at: 2021-10-02 21:30:47 UTC  
> Updated at: 2022-06-27 20:23:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2322  

head request takes ages.  
I might be wrong, but I suppose that async_read is waiting for a body, which of course is not there.  
Simple workaround might be using async_read_header... but in my opinion async_read should work anyway  
  
### Version of Beast  
  
306  
  
### Steps necessary to reproduce the problem  
  
```cpp  
#include <iostream>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
  
int main()  
{  
	boost::asio::io_context ctx;  
	boost::asio::ip::tcp::resolver resolver(ctx);  
	boost::beast::tcp_stream stream(ctx);  
	boost::beast::flat_buffer buffer_; // (Must persist between reads)  
	boost::beast::http::request<boost::beast::http::empty_body> req_;  
	boost::beast::http::response<boost::beast::http::string_body> res_;  
  
	resolver.async_resolve("www.google.com", std::to_string(80).c_str(), [&](boost::system::error_code ec, boost::asio::ip::tcp::resolver::results_type results) {  
		if (ec) {  
			std::cerr <<ec << std::endl;  
			return;  
		}  
		else {  
			stream.async_connect(results, [&](boost::system::error_code ec, boost::asio::ip::tcp::resolver::results_type::endpoint_type endpoint) {  
				if (ec) {  
					std::cerr << ec << std::endl;  
					return;  
				}  
				req_ = {};  
				req_.method(boost::beast::http::verb::head);  
				req_.target("/");  
				req_.set(boost::beast::http::field::host, "www.google.com");  
				req_.set(boost::beast::http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
				res_ = {};  
				buffer_.clear();  
				boost::beast::http::async_write(stream, req_, [&](boost::system::error_code ec, [[maybe_unused]] std::size_t bytes_transferred) {  
					if (ec) {  
						std::cerr << ec << std::endl;  
						return;  
					}  
					boost::beast::http::async_read(stream, buffer_, res_, [&](boost::system::error_code ec, [[maybe_unused]] std::size_t bytes_transferred) {  
						std::cerr << "done, transferred " << bytes_transferred << std::endl;  
						});  
					});  
			});  
		}  
	});  
  
	ctx.run();  
}  
```  
  
  
### All relevant compiler information  
  
Visual Studio 2019, building in Debug / 32 bit. Yes. 32 bit.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-02 23:01:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-932831319  

You have to tell the library that you don't expect a body, this is explained here:  
https://www.boost.org/doc/libs/1_77_0/libs/beast/doc/html/beast/more_examples/head_request_client.html  
and here:  
https://www.boost.org/doc/libs/1_77_0/libs/beast/doc/html/beast/ref/boost__beast__http__parser/skip/overload2.html

---

## Comment 2

> Username: foosoftsrlold  
> Created at: 2021-10-03 22:07:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933033144  

Hi Vinnie,  
thanks for the quick reply.  
IMHO it might ne more crearly stated here: beast/ref/boost__beast__http__async_read/overload2.html that the API is not compatible with HTTP requests which expose a "Content-Length", but have no body.  
  
First case, is the HEAD method, second one is the 304 response.  
This second case is a bit more tricky, as responses for revalidation requests (If-None-Match, If-Modified-Since,...) might be 2xx (body present) or 304 (no body), depending on revalidation outcome.  
  
IMHO delegating this logic to boost beast's user does more harm than good, as the HTML spec is quite strict and stable as to body presence in the response.   
Are custom exotheric method types the reason about this delegation? Does boost beast support custom methods not in http::verb?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-10-03 23:53:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933053177  

> the API is not compatible with HTTP requests which expose a "Content-Length", but have no body. First case, is the HEAD method, second one is the 304 response.  
  
But that's not correct. Beast understands perfectly that a 304 has no body:  
  
https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/http/impl/basic_parser.ipp#L513  
  
The problem with a response to HEAD is that Beast has no way to know that it is receiving a response to the HEAD request. The user has to inform the library - there is no other way around. All the other response codes should work, i.e. Beast will know that they do not have the body (see code above). You will note that every HTTP library is like this. For example, see here:  
https://github.com/nodejs/http-parser/blob/ec8b5ee63f0e51191ea43bb0c6eac7bfbff3141d/http_parser.h#L75

---

## Comment 4

> Username: foosoftsrlold  
> Created at: 2021-10-04 10:01:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933330938  

Ehm... sorry... I missed the HTTP 304 stuff.  
  
First of all.. using the async_write overload with "parser" argument and calling the skip() method on the parser does the trick indeed, and the whole thing boils down to async_write with "embedded parser" incompatibilty with HEAD requests.  
  
async_write of course knows that it's making an HEAD request, I'm pretty sure the code could be modified to automatically configure the parser (not that I could do it ;-)  
  
Possibly this missing bit of logic has not been written for a design choice... say separation of concerns or whatever.  
But to me, it looks like this bit of logic would make using beast just a bit simpler.  
  
Am i really that wrong?

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-10-04 10:53:57 UTC  
> Updated at: 2021-10-04 10:54:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933367811  

We do have internal discussions on design improvements that could be made.  
  
We’re not entirely happy with the relationship between parser and body_type, for example.  
  
So the door is certainly open if you have design changes to explore.  
  
In response to the above:  
  
I think our view that setting a flag on the parser (I am not expecting a body) and asking the parser to skip the body after receiving the header, logically amount to the same thing.   
  
Do we agree on that?

---

## Comment 6

> Username: foosoftsrlold  
> Created at: 2021-10-04 12:12:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933423344  

Hi madmongo,  
  
I can talk as a user, and not as a library developer, which I'm not (I wish I were).  
I understand that beast is more targeted to higher-level library writers, and not to the average developer, but the star count, (BTW, you have one more, right now) suggest that there are a *lot* of users which use it (as I do) for applications.  
  
If you ask me, I would find it much more reasonable to have an "I do it all" API (read_async HEAD aware) and two lower level APIs read_header, read_body: it would be a bit more obvious that it is up to the user to skip the body.  
  
Lastly, as I guess that 99% of people creates a "connection" class with a request, a response, a buffer, and a parser, I'd try to reduce headache and push more with "moves". Definitely for the request. Probably for the response. Not sure for the parser, as it is virtual (is it really needed?)  
  
In my dreams I would code this:  
  
http::read_async(std::move(request), [](error_code, response&& r) {  
...  
...  
});  
  
or for more elaborate use cases:  
  
http::read_async(std::move(request), parser_options, [](error_code, response&& r) {  
...  
...  
});  
  
Again, I'm not a library writer, not a template guy, so it is really possible I'm talking complete nonsense.

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-10-04 13:17:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933475882  

None of this is nonsense. I think our view is that users would love an API that looks a lot like python’s requests library.  
This is actually on my todo list.  
A good start to informing that library is to have some good real-world use cases, such as yours.

---

## Comment 8

> Username: foosoftsrlold  
> Created at: 2021-10-04 14:27:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933542062  

Well my use case right now is opening a few connections and stress test media origin servers.   
Good news... going 10Gbit and up is a breeze, and once you got acquainted with think-async... using beast is not that complicate. Definitely easier than baking your own asio based http library.  
  
So what is missing... is what you know is missing: high level.  
  
* Simpler APIs, even if they add CPU overhead  
  
And let me add:  
  
* HTTP Connection recycling  
  
I suppose that taking inspiration from a python library is a good idea (I see that requests is the stanrdard-the facto): API friendliness is very typical over there.  
  
Let me know if and how I can be any help.

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-10-04 14:31:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933545474  

I have some good news for you.  
  
- I've written all this stuff already (including connection recycling)  
- I do have it on my roadmap to produce such a library  
- You might just be the catalyst that gets this started.  
  
If I published an absolutely minimal subject-to-change library that you could experiment with and provide feedback, would you be happy to act as a lab rat user?

---

## Comment 10

> Username: foosoftsrlold  
> Created at: 2021-10-04 14:38:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933551988  

Go ahead

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-10-04 14:51:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933563700  

OK, I'm actually on holiday at the moment, so I'll start while my wife isn't looking. She'll get mad with me if she catches me working ;-)

---

## Comment 12

> Username: vinniefalco  
> Created at: 2021-10-04 16:15:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-933639975  

> So what is missing... is what you know is missing: high level.  
  
Yes you're right about that. Beast is intended to be the low level building block upon which these higher abstractions are written. A higher level library would handle the entire HEAD process from request to response for you, hiding the detail of knowing that the body is not expected.

---

## Comment 13

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2322#issuecomment-1008220722  

This issue has been open for a while with no activity, has it been resolved?
