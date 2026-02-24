# #2380 - Assert in stream_base::assign under load when using TLS [Closed]

> Username: XyFreak  
> Created at: 2022-01-19 13:11:43 UTC  
> Updated at: 2022-01-19 16:56:55 UTC  
> Closed at: 2022-01-19 16:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2380  

When reading and writing at the same time, occasionally BOOST_ASSERT trips in beast/core/detail/stream_base.hpp:111. The assertion trips when using `boost::base::ssl_stream<boost::beast::tcp_stream>` as sub-layer for `boost::beast::websocket::stream` but does not trip when using `boost::beast::tcp_stream` for plaintext websocket.  
  
The comment mentions two of the same asynchronous I/O operations being issued. I have confirmed that my code doesn't do that and wrote a test program that will demonstrate the issue.  
  
I have verified this issue with the following versions / compilers / platforms:  
* BOOST_BEAST_VERSION = 318 | compiler `gcc 11.2.1` linux64  
* BOOST_BEAST_VERSION = 322 | compiler `msvc 19.29.30139` win32 / win64  
  
I did find issue https://github.com/boostorg/beast/issues/2345 in which a similar issue was appearently fixed and the code that is "failing" now is part of the fix for that issue.  
  
The attached test program is not the prettiest but it triggers this issue relatively reliably for me.  
The program itself reads messages and discards them and writes messages continously (until it crashes) to a websocket echo server. You can compile it with -DWITHOUT_TLS=1 to disable TLS and see the issue disappearing. The assertion will be triggered by the client code.  
  
A quick overview over what fails (in case you don't want to look at the attached program because it's too large):  
```c++  
boost::asio::awaitable<void> go() {  
	boost::asio::co_spawn(executor(), read_loop(), boost::asio::detached);  
	boost::asio::co_spawn(executor(), write_loop(), boost::asio::detached);  
}  
  
boost::asio::awaitable<void> write_loop() {  
	while ( true ) {  
		co_await stream().async_write(boost::asio::buffer("This is just a test"), boost::asio::use_awaitable);  
	} // while ( ... )  
}  
  
boost::asio::awaitable<void> read_loop() {  
	boost::beast::flat_buffer rx_buffer;  
  
	while ( true ) {  
		const auto bytes_transferred = co_await stream().async_read(rx_buffer, boost::asio::use_awaitable);  
  
		rx_buffer.consume(bytes_transferred);  
	} // while ( ... )  
}  
```  
With the stream being an aforementioned `boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>>` stream connected to an echo server.  
  
[main.cpp.txt](https://github.com/boostorg/beast/files/7897296/main.cpp.txt)  
Build with gcc: `g++ -std=c++20 main.cpp -o test -lssl -lcrypto`

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-01-19 13:15:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2380#issuecomment-1016455098  

Thank you for the great and detailed bug report.  
Out of interest, have you compiled the program for clang with libc++?  
I will do it anyway to check. But I have noticed that clang's implementation of coroutines has been the most reliable for me so far.

---

## Comment 2

> Username: XyFreak  
> Created at: 2022-01-19 13:48:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2380#issuecomment-1016483110  

Thank you for your swift answer. I have not tried clang yet but that's a great idea and i'll try it immediately.

---

## Comment 3

> Username: XyFreak  
> Created at: 2022-01-19 14:17:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2380#issuecomment-1016508327  

In order to build with clang I had to update boost to 1.78 on my linux machine (BOOST_BEAST_VERSION -> 322). The issue is reproducible with clang as well.

---

## Comment 4

> Username: madmongo1  
> Created at: 2022-01-19 14:20:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2380#issuecomment-1016512109  

Yes, I have reproduced with clang.  
I also see segfaults when switching to native Asio stream types.  
You may wish to check my changes.  
  
https://github.com/beast-issues/2380

---

## Comment 5

> Username: XyFreak  
> Created at: 2022-01-19 15:08:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2380#issuecomment-1016560559  

Interestingly enough there seems to be issues with exceptions in coros. I switched to a `boost::asio::redirect_error`  
 completion token for some of the routines that might get interrupted when the timer cancels the current attempt.  
With that change in place the native Asio streams seem to work.  
  
https://github.com/XyFreak/2380

---

## Comment 6

> Username: XyFreak  
> Created at: 2022-01-19 15:25:33 UTC  
> Updated at: 2022-01-19 15:25:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2380#issuecomment-1016578057  

Ok i should've looked at the error codes:  
While the native asio streams don't crash, the read operation at some point fails with `asio.ssl.stream:2:unspecified system error` and sometimes you can see a `asio.ssl:336151548:sslv3 alert bad record mac (SSL routines, ssl3_read_bytes)` instead.

---

## Comment 7

> Username: XyFreak  
> Created at: 2022-01-19 16:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2380#issuecomment-1016632812  

Ok this is a case of RTFM. Native asio ssl streams _require_ the use of a strand and I didn't use one. I'm extremely sorry for having taken up your time >_<.  
  
Using a strand here fixes everything.

---

## Comment 8

> Username: madmongo1  
> Created at: 2022-01-19 16:56:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2380#issuecomment-1016667942  

When valuable things are learned, no-one's time is wasted. Happy to help.
