# #1071 - SSL websocket: after async_close(), read handler got cancellation error code instead of error::closed [Open]

> Username: vchang-akamai  
> Created at: 2018-03-17 05:22:40 UTC  
> Updated at: 2022-10-04 10:51:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1071  

(using top of develop branch)  
  
According to:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html  
> callers should continue reading until an error occurs. A read returning websocket::closed indicates a successful connection closure.   
  
Test scenario:  
  
Client and server both using SSL websockets and fully async Beast APIs.   
  
Client calls async_close().  Close handler gets success error code. Read handler gets operation canceled error code, instead of expected websocket::error::closed.  
  
Server's read handler was ok. It got the expected websocket::error::closed.  
  
I also put a print statement in the SSL's async_teardown() in websocket/impl/ssl.ipp.  It was reached in both client and server.  
  
I can send you my test programs (Actually, they're the same ones I sent you for #1012 earlier)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-17 15:44:13 UTC  
> Updated at: 2018-03-17 15:45:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1071#issuecomment-373929846  

I believe the documentation is in error. `websocket::error::closed` indicates that the remote host initiated the closing handshake, and that handshake was completed (Beast automatically responds to an unsolicited close frame by sending a close frame and tearing down the connection). Because the close was unsolicted, the read operation has to complete. It can't complete with success, since there is no message. Therefore, it has to complete as if it was canceled (since it was). There is even a test for this:  
https://github.com/boostorg/beast/blob/develop/test/beast/websocket/close.cpp#L390  
  
However, since you brought it up as an issue I am now wondering if this behavior should be changed. I'll leave this issue open for anyone to weigh in if they have thoughts for or against.  
  
This is the code which cancels a pending read when a close is initiated by the local host:  
https://github.com/boostorg/beast/blob/4d660a5e54dee428e99ec0520620e028c1975727/include/boost/beast/websocket/impl/read.ipp#L210

---

## Comment 2

> Username: hbailo  
> Created at: 2020-04-12 05:24:14 UTC  
> Updated at: 2020-04-12 05:24:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1071#issuecomment-612566379  

I had the same question as @vchang-akamai and took me some time to reach this issue. I think it would be helpful to indicate this behavior more precisely in the documentation of async_close(), just for clarification.  
Actual boost beast's doc says the same as in 2018:  
[]https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html

---

## Comment 3

> Username: akildemir  
> Created at: 2020-06-29 17:43:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1071#issuecomment-651265246  

@vinniefalco I'm getting this error in the completion handler of ```async_handshake()``` what about that hahaha. And the reason might be that I'm not recalling  the ```async_read()```again if I get an error and trying to re-establish the connection with io_context and everything and ```async_handshake()``` is the first function that gets called that belongs to ```websocket::stream``` after the connection gets closed by the server.  
  
Here is the full scenario:  
  
I am connection to a server that automatically closes the connection after 24 hr of stream and clients are expected initiate a new connection. When the server closes the connection I get ```stream truncated```error in the  in the completion handler of ```async_read``` therefore I don't recall the  ```async_read()```again. ```asio::io_context``` runs out of jobs and returns as expected. When this happens, I restart the whole chain again starting from ```tcp::resolver::async_resolve```  and restart the io and run. And I got ```handshake: Operation canceled```in completion handler of ```async_handshake()```.  Is my theory correct or there is something else goin on? Because this is preventing me to reconnect. Also surpassingly when this happens, io_context runs ot of jobs and restart the chain again and this time I got error on ssl handshake before even reaching to the handshake saying that "unspecified system error". LOL.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-06-30 12:59:57 UTC  
> Updated at: 2020-06-30 13:00:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1071#issuecomment-651774649  

@akildemir If I understand correctly, you are re-using the same websocket stream for the reconnection?  
  
This is not supported. You will need to destroy the stream and create a new one.  
  
One way to do this is to store the websocket::stream in an `optional<websocket<stream<>>>`. Or if you prefer you can simply destroy it by calling its destructor directly and then using in-place new.  
  
Obviously, there must be no outstanding async operations in progress on the stream at the time.  
  
It is not unusual to embed a websocket stream in some kind of "connection" object. For ease, I often prefer to destroy the entire connection object and create a new one in the case of a reconnect.  
  
R

---

## Comment 5

> Username: akildemir  
> Created at: 2020-06-30 13:26:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1071#issuecomment-651789489  

@madmongo1 yes i was trying to reconnect with the same stream object. Actually originally I was destroying the whole object and recreating it. Then i redesigned my wrapper around the stream object to be able to reuse it again for reconnection. Thought it was unnecessary to destroy and recreate the same object. I didn't know this wasn't supported. Thanks. I will revert it back to the original code. :)

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:34:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1071#issuecomment-1256880326  

Is this resolved?

---

## Comment 7

> Username: stavikpetr  
> Created at: 2022-10-04 07:37:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1071#issuecomment-1266529773  

Hello, I think I actually encountered the same issue just recently. So please, just to clarify - it is currently not possible to easily recognize graceful close from the client side by the error code in the async_read operation handler? For example, on Windows I am regularly receiving error code 995, (connection forcibly closed).

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2022-10-04 09:49:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1071#issuecomment-1266689647  

I think if you're the one closing the async read will get cancelled. Forcibly closed is another error though, that means the other side initiated the close of the websocket.

---

## Comment 9

> Username: stavikpetr  
> Created at: 2022-10-04 10:51:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1071#issuecomment-1266763730  

Hmm, that is interesting. In my case, if the server is initiating the close, the async_read handler will finish properly with boost::beast::websocket::error::closed, and in the case of client, I am not getting the cancelled error code that you are suggesting.  
  
Below, I have a very simple ws client, ws server (this server uses uwebsockets library). This should demonstrate what I have in mind.  
  
<details>  
<summary>Client code</summary>  
  
```C++  
#include <iostream>  
#include <string>  
#include <memory>  
#include <thread>  
#include <chrono>  
#include <atomic>  
  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/io_service.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast/core.hpp>  
  
struct ConnectionContext {  
	boost::asio::io_context ios;  
	boost::beast::websocket::stream<boost::asio::ip::tcp::socket> ws;  
	boost::beast::flat_buffer readBuffer;  
	  
	ConnectionContext() :ws(ios)  
	{  
	}  
};  
  
std::unique_ptr<ConnectionContext> createCC(const std::string& ip, int port)  
{  
	auto cc = std::make_unique<ConnectionContext>();  
  
	boost::asio::ip::tcp::resolver resolver(cc->ios);  
	boost::asio::connect(cc->ws.next_layer(), resolver.resolve(ip, std::to_string(port)));  
	cc->ws.handshake(ip + ":" + std::to_string(port), "/");  
  
	return std::move(cc);  
}  
  
void onRead(ConnectionContext* cc, boost::beast::error_code ec, size_t bytesRead)  
{  
	if (ec.value() != 0) {  
		if (ec == boost::beast::websocket::error::closed) {  
			std::cout << "<<< onRead() - graceful close received" << std::endl;  
			return;  
		}  
		else {  
			std::cout << "<<< onRead() - exception during read ... code: " << std::to_string(ec.value()) << std::endl;  
			return;  
		}  
	}  
  
	auto data = std::string((char*)cc->readBuffer.data().data(), cc->readBuffer.data().size());  
  
	std::cout << "<<< onRead() - message received: " << data << std::endl;  
  
	cc->readBuffer.clear();  
  
	// enqueue another async read  
	cc->ws.async_read(cc->readBuffer, boost::beast::bind_front_handler(&onRead, cc));  
}  
  
void runAsyncClient(ConnectionContext* cc)  
{  
	// enqueue first async read  
	cc->ws.async_read(cc->readBuffer, boost::beast::bind_front_handler(&onRead, cc));  
  
	// run the ios loop  
	cc->ios.run();  
}  
  
void onWrite(const std::string& msg, boost::beast::error_code ec, size_t bytesWritten)  
{  
	if (ec.value() != 0) {  
		std::cout << "--- message written with error" << std::endl;  
	}  
	else {  
		std::cout << ">>> message written: " << msg << std::endl;  
	}  
}  
  
void onClose(boost::beast::error_code ec)  
{  
	if (ec.value() != 0) {  
		std::cout << "--- close send failed" << std::endl;  
	}  
	else {  
		std::cout << ">>> close send successfull" << std::endl;  
	}  
}  
  
int main()  
{  
	std::string ip = "127.0.0.1";  
	auto port = 8000;  
  
	// create connection context - basically connect the client  
	std::cout << "--- Creating Connection context " << std::endl;  
	auto cc = createCC(ip, port);  
	std::cout << "--- Connection context created - client is connected" << std::endl;  
  
	// run the "client connection" in another thread  
	std::thread t([&]() {  
		runAsyncClient(cc.get());  
	});  
  
	// do some dummy communication  
	std::this_thread::sleep_for(std::chrono::seconds(1));  
	std::cout << "--- dummy subscribe" << std::endl;  
	std::string msg1("subscribe topic X");  
	cc->ws.async_write(boost::asio::buffer(msg1), boost::beast::bind_front_handler(&onWrite, msg1));  
  
	std::this_thread::sleep_for(std::chrono::seconds(5));  
	std::cout << "--- dummy unsubscribe" << std::endl;  
	std::string msg2("unsubscribe topic X");  
	cc->ws.async_write(boost::asio::buffer(msg2), boost::beast::bind_front_handler(&onWrite, msg2));  
  
	// close the connection  
	std::this_thread::sleep_for(std::chrono::seconds(5));  
	std::cout << "--- sending close" << std::endl;  
	cc->ws.async_close(boost::beast::websocket::close_code::normal, boost::beast::bind_front_handler(&onClose));  
  
	t.join();  
}  
```  
  
</details>  
  
<details>  
<summary>Server code</summary>  
  
```C++  
#include <iostream>  
#include <string>  
struct IUnknown;  
  
#define UWS_NO_ZLIB  
#define UWS_NO_SSL  
#define LIBUS_NO_SSL  
#define LIBUS_NO_LIBUV  
#undef ERROR  
#include <uwebsockets/App.h>  
  
class ClientContext {  
};  
  
void processMessage(auto* ws, std::string_view msg)  
{  
	std::cout << "<<< received message: " << msg << std::endl;  
  
	auto response = std::string("echo :") + std::string(msg);  
	ws->send(response, uWS::TEXT);  
  
	std::cout << ">>> response: " << response << std::endl;  
}  
  
uWS::App::WebSocketBehavior webSocketBehavior()  
{  
	uWS::App::WebSocketBehavior config;  
	config.compression = uWS::DISABLED;  
	config.maxPayloadLength = 100 * 1024;  
  
	config.open = [](auto* ws)  
	{  
		std::cout << "--- new client connected" << std::endl;  
	};  
  
	config.message = [](auto* ws, auto message, auto opCode) {  
		processMessage(ws, message);  
	};  
  
	config.close = [](auto* ws, auto code, auto message) {  
		std::cout << "--- performing client close callback" << std::endl;  
	};  
  
	return std::move(config);  
}  
  
int main()  
{  
	uWS::App app;  
	app.ws<ClientContext>("/*", std::move(webSocketBehavior()))  
		.listen(8000, [&](us_listen_socket_t* socket) {  
		if (socket) {  
			std::cout << "--- Server is listening on port 8000" << std::endl;  
		}  
		else {  
			std::cout << "--- Server failed to listen" << std::endl;  
		}  
	})  
	.run();  
	  
}  
```  
  
</details>  
  
With this code, the client finishes with message `onRead() - exception during read ... code: 995`.
