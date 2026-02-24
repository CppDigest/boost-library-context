# #45 - Question: WebSocket server implementation segfaults [Closed]

> Username: tiesjan  
> Created at: 2016-08-05 07:53:11 UTC  
> Updated at: 2016-09-25 18:40:43 UTC  
> Closed at: 2016-09-25 18:40:43 UTC  
> Url: https://github.com/boostorg/beast/issues/45  

Hey there,  
  
I saw in the README I can ask questions here, so I'll give it a try. I just started to implement an async WebSocket server. I've tried to keep it as close to my implementation of an async `boost::asio::ip::tcp` socket server, but still followed Beast's documentation as much as possible. This is so I can keep it simple for myself, as I'm still a fanatic hobbyist C++ at this point and the project is heavily in development. :-)  
  
It works as following:  
- [WebSocketAcceptor](https://github.com/Hefting/HarmonIQ/blob/master/src/network/WebSocketAcceptor.cpp)  
  1. it sets up an end point, opens a socket, binds to it and starts listening - lines 36-63  
  2. it starts an async accept at the `io_service` - line 73  
  3. when one is accepted, it accepts it on the WebSocket layer, creates/starts a WebSocketSession and starts another async accept at the `io_service` - lines 88-101  
- [WebSocketSession](https://github.com/Hefting/HarmonIQ/blob/master/src/network/WebSocketSession.cpp)  
  1. Once created, it registers itself at the MessageCenter (currently an echo server) and starts an async read - lines 51-53 and line 73  
  2. Once a message is received, it is stored in a string and processed at this MessageCenter (which just echos it back to the session using the Write method) - lines 94-99  
  
So far so good. Once I connect from the console of a browser, it accepts the connection and I can write a message, which shows up in the console as a received message. However, as soon as I connect again (whether it's from another computer or another browser) the program segfaults inside Beast library. Below you can find some GDB output.  
  
If it's not too much, could one of you maybe point me in the right direction? I just don't see it, but I really would like to get it to work with Beast. Also, other tips are welcome as well, still learning here. :-)  
  
---  
  
GDB output:  
  
```  
Thread 3 "HarmonIQ" received signal SIGSEGV, Segmentation fault.  
[Switching to Thread 0x7ffff6345700 (LWP 4591)]  
0x00000000005d5f84 in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::build_response<beast::http::string_body, beast::http::basic_headers<std::allocator<char> > > (this=0x97c210, req=...) at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:1000  
1000        (*d_)(res);  
Missing separate debuginfos, use: dnf debuginfo-install boost-system-1.60.0-7.fc24.x86_64 libgcc-6.1.1-3.fc24.x86_64 libstdc++-6.1.1-3.fc24.x86_64  
```  
  
Head of the GDB stack trace:  
  
```  
#0  0x00000000005d5f84 in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::build_response<beast::http::string_body, beast::http::basic_headers<std::allocator<char> > > (this=0x97c210, req=...) at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:1000  
#1  0x00000000005d020e in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::accept<beast::http::string_body, beast::http::basic_headers<std::allocator<char> > > (this=0x97c210, req=..., ec=...) at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:306  
#2  0x00000000005cbe60 in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::accept<boost::asio::null_buffers> (  
    this=0x97c210, buffers=..., ec=...) at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:259  
#3  0x00000000005c9771 in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::accept (this=0x97c210, ec=...)  
    at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:205  
#4  0x00000000005c140f in network::WebSocketAcceptor::handle_async_accept (this=0x97c1e8, error_code=...) at /home/tiesjan/Documents/HarmonIQ/src/network/WebSocketAcceptor.cpp:90  
```  
  
_the rest of the stack trace is `boost::asio` invoking `WebSocketAcceptor.handle_async_accept`_

---

## Comment 1

> Username: socmag  
> Created at: 2016-08-05 07:56:19 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-237781416  

I'd love to see that working as well  
  
On Aug 5, 2016 9:53 AM, "Ties Jan Hefting" notifications@github.com wrote:  
  
> Hey there,  
>   
> I saw in the README I can ask questions here, so I'll give it a try. I  
> just started to implement an async WebSocket server. I've tried to keep it  
> as close to my implementation of an async boost::asio::ip::tcp socket  
> server, but still followed Beast's documentation as much as possible. This  
> is so I can keep it simple for myself, as I'm still a fanatic hobbyist C++  
> at this point and the project is heavily in development. :-)  
>   
> It works as following:  
> - WebSocketAcceptor  
>   https://github.com/Hefting/HarmonIQ/blob/master/src/network/WebSocketAcceptor.cpp  
>   1. it sets up an end point, opens a socket, binds to it and starts  
>      listening - lines 36-63  
>   2. it starts an async accept at the io_service - line 73  
>   3. when one is accepted, it accepts it on the WebSocket layer,  
>      creates/starts a WebSocketSession and starts another async accept at the  
>      io_service - lines 88-101  
> - WebSocketSession  
>   https://github.com/Hefting/HarmonIQ/blob/master/src/network/WebSocketSession.cpp  
>   1. Once created, it registers itself at the MessageCenter  
>      (currently an echo server) and starts an async read - lines 51-53 and line  
>      73  
>   2. Once a message is received, it is stored in a string and  
>      processed at this MessageCenter (which just echos it back to the session  
>      using the Write method) - lines 94-99  
>   
> So far so good. Once I connect from the console of a browser, it accepts  
> the connection and I can write a message, which shows up in the console as  
> a received message. However, as soon as I connect again (whether it's from  
> another computer or another browser) the program segfaults inside Beast  
> library. Below you can find some GDB output.  
>   
> If it's not too much, could one of you maybe point me in the right  
> direction? I just don't see it, but I really would like to get it to work  
>   
> ## with Beast. Also, other tips are welcome as well, still learning here. :-)  
>   
> GDB output:  
>   
> Thread 3 "HarmonIQ" received signal SIGSEGV, Segmentation fault.  
> [Switching to Thread 0x7ffff6345700 (LWP 4591)]  
> 0x00000000005d5f84 in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::build_response<beast::http::string_body, beast::http::basic_headers<std::allocator<char> > > (this=0x97c210, req=...) at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:1000  
> 1000        (*d_)(res);  
> Missing separate debuginfos, use: dnf debuginfo-install boost-system-1.60.0-7.fc24.x86_64 libgcc-6.1.1-3.fc24.x86_64 libstdc++-6.1.1-3.fc24.x86_64  
>   
> Head of the GDB stack trace:  
>   
> #0  0x00000000005d5f84 in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::build_response<beast::http::string_body, beast::http::basic_headers<std::allocator<char> > > (this=0x97c210, req=...) at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:1000  
> #1  0x00000000005d020e in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::accept<beast::http::string_body, beast::http::basic_headers<std::allocator<char> > > (this=0x97c210, req=..., ec=...) at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:306  
> #2  0x00000000005cbe60 in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::acceptboost::asio::null_buffers (  
>     this=0x97c210, buffers=..., ec=...) at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:259  
> #3  0x00000000005c9771 in beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >::accept (this=0x97c210, ec=...)  
>     at /home/tiesjan/Documents/HarmonIQ/lib/beast/websocket/impl/stream.ipp:205  
> #4  0x00000000005c140f in network::WebSocketAcceptor::handle_async_accept (this=0x97c1e8, error_code=...) at /home/tiesjan/Documents/HarmonIQ/src/network/WebSocketAcceptor.cpp:90  
>   
> _the rest of the stack trace is boost::asio invoking  
> WebSocketAcceptor.handle_async_accept_  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/vinniefalco/Beast/issues/45, or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AGQZ0ol_bVQrtI-_4pfC_ozX4rx7T1DAks5qcuvogaJpZM4JdcFO  
> .

---

## Comment 2

> Username: tiesjan  
> Created at: 2016-08-05 08:21:00 UTC  
> Updated at: 2016-08-07 19:44:47 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-237786058  

Are you talking reproducing it using my program? Or are you also interested in the answer?  
  
---  
  
If reproducing: my program only depends on Boost and a few POSIX headers.  
  
Building and running:  
  
```  
mkdir build && cd build  
cmake -DCMAKE_BUILD_TYPE=DEBUG ..  
make  
```  
  
Then inside the build there's a bin/ directory. Inside it there's a binary `HarmonIQ` to run.  
  
Run in an empty tab in the web browser console twice:  
  
```  
var ws = new WebSocket("ws://localhost:3553");  
```

---

## Comment 3

> Username: socmag  
> Created at: 2016-08-05 08:24:20 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-237786716  

I'd love to see a working WebSocket server example.  Yours or something  
else.  I'm currently using a different library and it would be really nice  
to compare performance.  
  
On Aug 5, 2016 10:21 AM, "Ties Jan Hefting" notifications@github.com  
wrote:  
  
> Are you talking reproducing it using my program? Or are you also  
>   
> ## interested in the answer?  
>   
> If reproducing: my program only depends on Boost and a few POSIX headers.  
>   
> Building and running:  
>   
> mkdir build && cd build  
> cmake -DCMAKE_BUILD_TYPE=DEBUG ..  
> make  
>   
> Then inside the build there's a bin/ directory. Inside it there's a binary  
> HarmonIQ to run.  
>   
> Run in an empty tab in the web browser console twice:  
>   
> var ws = new WebSocket("ws://localhost:3553"); ws.onmessage = function(msg) { console.log(msg.data); };  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> https://github.com/vinniefalco/Beast/issues/45#issuecomment-237786058,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AGQZ0vWc02h6_uRR1F-c2UPLcwU1ftAgks5qcvJtgaJpZM4JdcFO  
> .

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-08-05 14:50:37 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-237871106  

There are two complete WebSocket echo server implementations already in Beast, they are in the test directory. These servers are used to pass the WebSocket Autobahn|Testsuite tests. See:  
  
https://github.com/vinniefalco/Beast/tree/master/test/websocket  
https://github.com/vinniefalco/Beast/blob/master/test/websocket/websocket_async_echo_peer.hpp  
https://github.com/vinniefalco/Beast/blob/master/test/websocket/websocket_sync_echo_peer.hpp

---

## Comment 5

> Username: socmag  
> Created at: 2016-08-05 16:11:38 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-237893190  

Okay great,  must have missed them.  
  
Thanks!  
  
On Aug 5, 2016 4:50 PM, "Vinnie Falco" notifications@github.com wrote:  
  
> There are two complete WebSocket echo server implementations already in  
> Beast, they are in the test directory. These servers are used to pass the  
> WebSocket Autobahn|Testsuite tests. See:  
>   
> https://github.com/vinniefalco/Beast/tree/master/test/websocket  
> https://github.com/vinniefalco/Beast/blob/master/test/websocket/websocket_  
> async_echo_peer.hpp  
> https://github.com/vinniefalco/Beast/blob/master/  
> test/websocket/websocket_sync_echo_peer.hpp  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> https://github.com/vinniefalco/Beast/issues/45#issuecomment-237871106,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AGQZ0mcAAYi1H5jq0LSE9mdZJziuNNP2ks5qc02-gaJpZM4JdcFO  
> .

---

## Comment 6

> Username: tiesjan  
> Created at: 2016-08-05 20:55:33 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-237963890  

Thanks, I did however already look into these tests and couldn't find much difference with my code. I will take another look at it this weekend and will let you know if I have more questions.

---

## Comment 7

> Username: tiesjan  
> Created at: 2016-08-07 11:06:48 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-238076347  

Seems like I solved this problem, the changes can be found [here](https://github.com/Hefting/HarmonIQ/commit/1c61f92c310072868a8392fbc412e31ec3fef0fc).  
  
In the old code I took [this server example](http://vinniefalco.github.io/beast/beast/websocket/connecting.html) and converted it into an [asynchronous version](https://github.com/Hefting/HarmonIQ/commit/1c61f92c310072868a8392fbc412e31ec3fef0fc?diff=split#diff-7fa93f1a176d905e33971e7b5ec79015L81). Once the WebSocket handshake was accepted as well, I [moved](https://github.com/Hefting/HarmonIQ/commit/1c61f92c310072868a8392fbc412e31ec3fef0fc#diff-7fa93f1a176d905e33971e7b5ec79015L108)  `incoming_sock` (a `beast::websocket::stream`) to my `WebSocketSession` class.  
  
In the new code I accept a `boost::asio::ip::tcp::socket` and [wrap](https://github.com/Hefting/HarmonIQ/commit/1c61f92c310072868a8392fbc412e31ec3fef0fc?diff=split#diff-7fa93f1a176d905e33971e7b5ec79015R99) it inside a `beast::websocket::stream` before moving it to a `WebSocketSession`.  
  
Could moving the `beast::websocket::stream` indeed be the problem?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-08-07 17:41:34 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-238096573  

Ah, there very well could be a bug in the move constructor for `stream`!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-08-07 17:44:06 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-238096721  

`beast::websocket::stream_base` owns `d_` (implicated in your crash), and `stream_base` uses a default move constructor so I don't think thats the problem. :-(

---

## Comment 10

> Username: vinniefalco  
> Created at: 2016-08-07 18:26:17 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-238099209  

@Hefting If you could please put together the smallest program which can reproduce the problem, I am confident we can get it sorted.

---

## Comment 11

> Username: tiesjan  
> Created at: 2016-08-07 19:48:01 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-238103968  

I see, then that's probably not it...  
  
I created a [simple implementation](https://gist.github.com/Hefting/e107103be39e311d3fb9d4cd321c0d81). It compiles as follows:  
  
```  
g++ -g -std=c++14 -Ipath/to/beast/ -o websock_serv beast_websock_server.cpp -lboost_system  
```  
  
If I run the following twice in the browser console, it segfaults:  
  
```  
var ws = new WebSocket("ws://localhost:3553/");  
```

---

## Comment 12

> Username: vinniefalco  
> Created at: 2016-09-25 15:20:42 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-249427598  

I cannot reproduce your issue, is this still a problem for you?

---

## Comment 13

> Username: tiesjan  
> Created at: 2016-09-25 18:40:43 UTC  
> Url: https://github.com/boostorg/beast/issues/45#issuecomment-249438354  

I redesigned some parts of my program, so I worked around the problem. Not sure if it persists, closing for now.
