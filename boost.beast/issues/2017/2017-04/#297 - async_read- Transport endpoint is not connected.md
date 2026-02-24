# #297 - async_read: Transport endpoint is not connected [Closed]

> Username: matthijs  
> Created at: 2017-04-05 11:59:41 UTC  
> Updated at: 2017-06-19 17:51:29 UTC  
> Closed at: 2017-06-19 17:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/297  

Hi,  
  
When running the websocket_echo example, I get the following output when connecting to it with a simple program that connects to the server, sends a message and receive the message and then closes the link:  
  
[#1 127.0.0.1:59662] async_read: Transport endpoint is not connected  
  
Sample:  
```c++  
beast::websocket::stream<boost::asio::ip::tcp::socket&> ws {sock};  
ws.handshake("127.0.0.1", "/");  
ws.write(boost::asio::buffer(std::string("Hello, World!")));  
  
// Receive the message  
beast::streambuf sb;  
beast::websocket::opcode op;  
ws.read(op, sb);  
  
std::this_thread::sleep_for(std::chrono::milliseconds(500));  
std::cout << "Resp: " << beast::to_string(sb.data()) << std::endl;  
  
ws.close(beast::websocket::close_code::normal);  
```  
  
When closing the link I expected no output because of the following lines (examples/websocket_async_echo_server.hpp:318):  
```c++  
if(ec == beast::websocket::error::closed)  
  return;  
```  
  
I'm not sure if this is a bug or a misunderstanding on my side. Let me know.  
  
Regards, Matthijs

---

## Comment 1

> Username: JoelKatz  
> Created at: 2017-04-05 15:50:39 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-291905780  

@matthijs  So you close the TCP connection without properly closing the websocket connection or giving the server a chance to properly close it?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-04-05 23:15:49 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292024085  

Is that the complete source code to your program? It looks like things are missing.

---

## Comment 3

> Username: matthijs  
> Created at: 2017-04-06 06:29:26 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292081206  

Hi,  
  
@JoelKatz I'm looking into that.  
  
My test program:  
```c++  
#include <iostream>  
  
#include <beast/version.hpp>  
#include <beast/core/to_string.hpp>  
#include <beast/websocket.hpp>  
  
int main(int argc, char **argv) {  
  
	std::cout << "Using beast version: " << BEAST_VERSION_STRING << std::endl;  
  
	boost::asio::io_service ios;  
	boost::asio::ip::tcp::resolver r{ios};  
	boost::asio::ip::tcp::socket sock {ios};  
	boost::asio::connect(sock,  
			r.resolve(boost::asio::ip::tcp::resolver::query{"127.0.0.1", "12345"}));  
  
	// Websocket connect  
	beast::websocket::stream<boost::asio::ip::tcp::socket&> ws {sock};  
	ws.handshake("127.0.0.1", "/");  
	ws.write(boost::asio::buffer(std::string("Hello, World!")));  
  
	// Receive the message  
	beast::streambuf sb;  
	beast::websocket::opcode op;  
	ws.read(op, sb);  
  
	std::cout << "Resp: " << beast::to_string(sb.data()) << std::endl;  
  
	ws.close(beast::websocket::close_code::normal);  
  
	return 0;  
}  
```

---

## Comment 4

> Username: matthijs  
> Created at: 2017-04-06 07:00:57 UTC  
> Updated at: 2017-04-06 07:38:47 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292086735  

@JoelKatz I now added the following before 'return 0;' in the above program:  
  
```c++  
    ws.lowest_layer().shutdown(boost::asio::ip::tcp::socket::shutdown_both);  
    ws.lowest_layer().close();  
```  
  
According to the documentation that should properly close the link.  
  
I got the same results, forgot to add that to this message.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-04-06 12:32:40 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292159392  

You don't need to call `shutdown` or `close` on the lowest layer, that is handled for you. Can you please provide a link to the documentation where it says to call those functions, so I can fix it? Thanks.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-04-06 12:35:01 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292159922  

Which version of Beast?

---

## Comment 7

> Username: matthijs  
> Created at: 2017-04-06 12:40:26 UTC  
> Updated at: 2017-04-06 13:02:11 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292161334  

Version: 1.0.0-b31 (clone of the master branch, currently at the latest commit)  
  
I added shutdown and close to see if it made the warning / error go away:  
[#1 127.0.0.1:59662] async_read: Transport endpoint is not connected

---

## Comment 8

> Username: harrishancock  
> Created at: 2017-04-06 16:51:06 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292235690  

According to this page:  
  
http://vinniefalco.github.io/beast/beast/websocket/control.html  
  
> After initiating a close, it is necessary to continue reading messages until receiving the error error::closed. This is because the remote peer may still be sending message and control frames before it receives and responds to the close frame.  
  
Perhaps the "transport endpoint is not connected" error occurs because the server attempts to reply to the client after the client's websocket stream is destroyed?

---

## Comment 9

> Username: matthijs  
> Created at: 2017-04-06 18:32:49 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292267218  

Good call, but after trying another read with (in the client):  
```c++  
ws.read(op, sb);  
```  
The client blocked on the read call.  
  
I modified the example 'websocket-example' to connect to the websocket_echo server. And still got the same message (Transport endpoint is not connected).  
  
So the overal question is:  
How to properly close the websocket connection and how to handle it on the server side?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-04-06 19:06:22 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292280224  

Good observation @harrishancock ! FYI, I am on a laptop in a hotel for the next few days so I don't have the full power of my desktop development environment, thanks for helping.  
  
@matthijs If you could post your entire program (including the added call to `ws.read`) that would be helpful!  
  
Also, I know that this stuff works. The WebSocket example client works as does the echo server. So there is something in your code that we just haven't figured out yet, but soon I am sure.

---

## Comment 11

> Username: matthijs  
> Created at: 2017-04-06 19:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292286676  

@vinniefalco no problem, here it is:  
  
```c++  
#include <iostream>  
  
#include <beast/version.hpp>  
#include <beast/core/to_string.hpp>  
#include <beast/websocket.hpp>  
  
int main(int argc, char **argv) {  
  
	std::cout << "Using beast version: " << BEAST_VERSION_STRING << std::endl;  
  
	boost::asio::io_service ios;  
	boost::asio::ip::tcp::resolver r{ios};  
	boost::asio::ip::tcp::socket sock {ios};  
	boost::asio::connect(sock,  
			r.resolve(boost::asio::ip::tcp::resolver::query{"127.0.0.1", "6000"}));  
  
	// Websocket connect  
	beast::websocket::stream<boost::asio::ip::tcp::socket&> ws {sock};  
	ws.handshake("127.0.0.1", "/");  
	ws.write(boost::asio::buffer(std::string("Hello, World!")));  
  
	// Receive the message  
	beast::streambuf sb;  
	beast::websocket::opcode op;  
	ws.read(op, sb);  
  
	std::cout << "Resp: " << beast::to_string(sb.data()) << std::endl;  
  
	// Initiate close  
	ws.close(beast::websocket::close_code::normal);  
  
	// Continue reading because we initiate a close  
	try {  
		ws.read(op, sb);  
	} catch(const boost::system::system_error& e) {  
		std::cout << e.what() << std::endl;  
	}  
  
	return 0;  
}  
```  
Output:  
matthijs@hammer:~/smclient/build$ ./smclient  
Using beast version: 1.0.0-b31  
Resp: Hello, World!  
WebSocket connection closed normally  
  
I'm not sure what mistake I made but the above code is working. I don't get an error message on the server side anymore. Thanks @harrishancock and @vinniefalco for the help!  
  
@vinniefalco Looking at websocket_example.cpp after sending the close there is not a final read. With the documentation in mind shouldn't it be there?

---

## Comment 12

> Username: harrishancock  
> Created at: 2017-04-06 21:57:09 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292336008  

@matthijs glad it works! I think what I pointed out was what @JoelKatz was getting at, too. My takeaway is:  
   1. `websocket::stream::close(close_code)` performs only the first half of the WebSocket closing handshake. The second half is the `read()`.  
   2. Therefore, without the `read()` call, the `websocket::stream` destructor implicitly closes the underlying TCP socket before the closing handshake is complete.  
  
@vinniefalco perhaps this points out an asymmetry in the Beast.WebSocket API? The websocket RFC refers to both the opening and closing protocols as "handshakes", but Beast only implements the opening handshake as a single call (`websocket::stream::handshake()`). The closing handshake appears to be two calls. I'm not sure that's necessarily a problem in need of a solution, though.  
  
Glad to help!

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-04-07 12:52:46 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292528282  

@harrishancock Yep you are right, the websocket example is missing the calls to read!  
  
@matthijs Technically, you have to call `read` in a loop until it returns an error.

---

## Comment 14

> Username: JoelKatz  
> Created at: 2017-04-07 21:07:29 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-292652045  

Notionally, the other side may have sent you all kinds of stuff before it received the shutdown instructions. So if you want to do a normal close, you have to read all of it. The same is true for TCP. (Of course, precisely what happens when you don't do exactly this varies.)

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-06-08 20:08:12 UTC  
> Url: https://github.com/boostorg/beast/issues/297#issuecomment-307213047  

@harrishancock @matthijs Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!
