# #2978 - Not getting connections disconnected message on server [Closed]

> Username: shivank1111  
> Created at: 2025-02-04 02:50:34 UTC  
> Updated at: 2025-02-07 07:24:17 UTC  
> Closed at: 2025-02-07 07:24:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2978  

BOOST_VERSION: 356  
  
I have combined the `advanced_server_flex.cpp` and `chat_multi` code to create a program that enables message broadcasting and supports both SSL and plain connections.    
  
The issue I am facing is that I am unable to receive the error message (`ec.message()`) when the client disconnects from the server due to internet issues. The error message only appears in the `on_read` function after the internet connection is restored on the client side.    
  
To resolve this, I attempted to add the `async_ping` code (which I will attach below) and executed both `async_ping` and `async_write` within a strand. However, that small portion of the `async_ping` code is causing my program to crash.    
  
Can you help me ensure that the error message appears in the `on_read` function by default? The Beast library should detect and provide the error message, but it is not doing so.    
  
I am writing the below code inside the "on_read()" function, after clearing the buffer(buffer_.consume(buffer_.size());) and before calling "do_read();"  
  
```  
ws_.async_ping( "ping", [](beast::error_code ec) {  
				if (ec) {  
					std::cerr << "Ping error: " << ec.message() << std::endl;  
					return;  
				}  
				std::cout << "Sent ping" << std::endl;  
			});  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2025-02-04 05:39:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2978#issuecomment-2632926010  

Have you tried using automatic ping by setting appropriate timeout values? [websocket::stream::set_option (2 of 3 overloads)](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option/overload2.html)  
That way, `async_read` will handle pings in the background, eliminating the need to send ping messages manually.  
  
> To resolve this, I attempted to add the async_ping code (which I will attach below) and executed both async_ping and async_write within a strand. However, that small portion of the async_ping code is causing my program to crash.  
  
I need more information to help with this. There’s a good chance you’re facing a concurrency issue. If you’re using a multi-threaded executor, reduce it to a single thread and see if the problem persists.

---

## Comment 2

> Username: shivank1111  
> Created at: 2025-02-04 05:51:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2978#issuecomment-2632939159  

Hi @ashtum , I am passing like this for timeout value:  
  
// Set suggested timeout settings for the websocket  
			beast::websocket::stream_base::timeout opt  
			{  
				std::chrono::seconds(30), // handshake_timeout  
				std::chrono::seconds(30), // idle_timeout  
				true  
			};  
			ws_.set_option(opt);  
  
  
I am using single thread to run the code, still it is crashing.  
  
Our advanced_server_flex.cpp is not getting the error message when the client is getting disconnected. To get that message I had added the "async_ping" but that part is crashing the code.

---

## Comment 3

> Username: ashtum  
> Created at: 2025-02-04 05:56:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2978#issuecomment-2632945375  

You cannot enable automatic ping and use `async_ping` simultaneously.  
  
> Our advanced_server_flex.cpp is not getting the error message when the client is getting disconnected. To get that message I had added the "async_ping" but that part is crashing the code.  
  
You don't need `async_ping`; if you enable automatic ping, `async_read` will complete with an error after the timeout.

---

## Comment 4

> Username: shivank1111  
> Created at: 2025-02-04 06:16:44 UTC  
> Updated at: 2025-02-04 06:16:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2978#issuecomment-2632976519  

@ashtum , I have a doubt. If the client gets disconnected at the 10th second but we only receive the closed message after the timeout is complete, how can we notify the client that the channel is closed without waiting for that long?  
Is there an alternative approach to detect a network disconnection on the server side as soon as the client loses connectivity?

---

## Comment 5

> Username: ashtum  
> Created at: 2025-02-04 06:47:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2978#issuecomment-2633020385  

The specified timeout is the duration the stream object waits to receive a pong message. After this timeout, the connection is considered disconnected. There is no other way to determine a disconnection (otherwise, ping/pong messages would not be necessary).  
You can adjust the timeout to suit your needs; however, you should consider the natural latency that clients might experience. For example, if they are using mobile devices, a disconnect lasting a few seconds might be normal.

---

## Comment 6

> Username: shivank1111  
> Created at: 2025-02-04 07:10:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2978#issuecomment-2633057822  

Okay @ashtum , thanks for the help.
