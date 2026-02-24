# #1109 - Examples store a dangerous reference to string [Closed]

> Username: snahmad  
> Created at: 2018-04-27 12:14:57 UTC  
> Updated at: 2018-05-09 01:23:19 UTC  
> Closed at: 2018-05-09 01:23:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1109  

Ignore this.

---

## Comment 1

> Username: snahmad  
> Created at: 2018-04-27 14:02:00 UTC  
> Updated at: 2018-04-27 14:02:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-384979206  

Let me fix some compilation issue in my HttpServer class. sorry posted directly from our internal libraries. Let me write simple HttpServerTest program which uses HttpServer class and start and stop in a loop. to check this class in isolation. please wait. I will upload update HttpServer code.

---

## Comment 2

> Username: snahmad  
> Created at: 2018-04-27 16:06:58 UTC  
> Updated at: 2018-04-27 16:08:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385017369  

How can use boost::asio::io_context with boost::thread or std::thread  
  
I need to write simple HttpServer class with start and stop method based on  
  
https://github.com/boostorg/beast/blob/develop/example/http/server/async/http_server_async.cpp  
  
non blocking start method call.  
  
on stop Do Ineed to delete io_context ???

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-27 16:23:23 UTC  
> Updated at: 2018-04-27 16:23:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385021488  

The advanced server examples demonstrate how to stop the server:  
https://github.com/boostorg/beast/blob/5abac8b380ffcd914126c5019c980366c5a53f68/example/advanced/server/advanced_server.cpp#L832

---

## Comment 4

> Username: snahmad  
> Created at: 2018-04-28 13:20:45 UTC  
> Updated at: 2018-04-28 13:22:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385175511  

Not working for me. When Http Server start running. the request any http get cause it exceptions.  
See these HttpServer.h and HttpServer.cpp and AsyncHttpServerTest.cpp files.  
  
kindly help. I am stuck. I want to run io_context in a thread not blocking call.  
  
void HttpServer::start(const char* ip_address, uint16_t port, std::string doc_root)  
{  
	auto const address = boost::asio::ip::make_address(ip_address);  
	// Create and launch a listening port  
	listener_ = std::make_shared<listener>(  
		ioc_,  
		tcp::endpoint{ address, port },  
		doc_root);  
  
	listener_->run();  
	thread_ = std::thread([&]() {  
		ioc_.run();  
	});  
  
  
}  
void HttpServer::stop()  
{  
	ioc_.stop();  
	  
	if (thread_.joinable()) {  
		thread_.join();  
	}  
  
	listener_.reset();  
}  
  
  
  
[HttpServer.zip](https://github.com/boostorg/beast/files/1957735/HttpServer.zip)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-28 14:13:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385179051  

>Not working for me  
  
Does the unmodified advanced server example work for you?

---

## Comment 6

> Username: snahmad  
> Created at: 2018-04-28 14:17:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385179342  

The modified code is not working for me. kindly have look at code an run and send HTTP get or post request to this HTTP server, it cause exception.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-04-28 15:13:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385183086  

You should start with the unmodified advanced server, and then apply your changes one at a time until you find out what is causing the crash.

---

## Comment 8

> Username: snahmad  
> Created at: 2018-04-28 22:03:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385208498  

sure. can you try running my HttpServer class and check the code please.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-04-28 22:50:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385210622  

No, because you did not provided the "minimum, complete, and verifiable" sample:  
https://stackoverflow.com/help/mcve  
  
For example, what is `ARLErrorCode_e`? Please provide a minimal program that exhibits the problem.

---

## Comment 10

> Username: snahmad  
> Created at: 2018-04-29 10:33:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385241594  

Have look at https://github.com/boostorg/beast/files/1957735/HttpServer.zip contains minimal code.  
  
ignore first code I pasted.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-04-29 13:30:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385251641  

The object which `doc_root_` references is a local variable in the function `start`, which goes out of scope when that function returns. Change all instances of `std::string const& doc_root_` to `std::string const doc_root_`.

---

## Comment 12

> Username: snahmad  
> Created at: 2018-04-29 15:14:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385258390  

ok, Thanks working. close this thread.  
I guess same can apply to  
https://github.com/boostorg/beast/blob/develop/example/http/server/fast/http_server_fast.cpp example.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2018-04-29 15:24:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385259083  

This is not actually a bug in Beast, but I think the examples will be more robust if they use a shared string instead of a dangerous string reference, so that other people don't make the same mistake if they do what you did and modify the code. I'll keep this issue open since it reflects a change that will go into the next pull request, so it can be tracked and referenced.

---

## Comment 14

> Username: snahmad  
> Created at: 2018-04-30 21:00:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1109#issuecomment-385527150  

ok.
