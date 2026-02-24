# #2042 - http::read is slow [Closed]

> Username: canhld94  
> Created at: 2020-07-30 05:56:11 UTC  
> Updated at: 2020-07-31 06:58:00 UTC  
> Closed at: 2020-07-31 06:58:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2042  

I'm building an inference server that receives an image from a client and returns an object detection response in JSON format. I started with `http_server_sync.cpp` example and added my code that handles `POST` request with image data. Everything is fine, the server functions as I expected, however, when I profile the code, it turns out that reading from the socket is the bottle-neck of my server. Below is my profiling code for `do_session`. `PROFILE` is just a Marco to measure the execution time of a code block.  
  
```C++  
//! DON'T use it recursively. If you do it recursively, only read the innermost result  
std::chrono::time_point<std::chrono::system_clock> PROFILE_start;  
std::chrono::time_point<std::chrono::system_clock> PROFILE_end;  
std::chrono::duration<double,std::milli> PROFILE_elapsed_seconds;  
#define PROFILE(NAME,...) \  
      PROFILE_start = std::chrono::system_clock::now();\  
      __VA_ARGS__ \  
      PROFILE_end = std::chrono::system_clock::now(); \  
      PROFILE_elapsed_seconds = PROFILE_end - PROFILE_start; \  
      std::cout <<"[::PROFILE] " << NAME << ": " << PROFILE_elapsed_seconds.count() << " ms" << std::endl; \  
  
...  
  
void  
do_session(  
    tcp::socket& socket)  
{  
    bool close = false;  
    beast::error_code ec;  
  
    // This buffer is required to persist across reads  
    beast::flat_buffer buffer;  
    // Try to reserve buffer first  
  
    // This lambda is used to send messages  
    send_lambda<tcp::socket> lambda{socket, close, ec};  
  
    for(;;)  
    {  
        // Read a request  
        //! Very slow reading from socket -> why?  
        PROFILE ("read request",  
        http::request<http::string_body> req;  
        http::read(socket, buffer, req, ec);  
        ); //! PROFILE  
        if(ec == http::error::end_of_stream)  
            break;  
        if(ec)  
            return fail(ec, "read");  
  
        // Send the response  
        PROFILE ("handle request",  
        handle_request(std::move(req), lambda);  
        ); //! PROFILE  
        if(ec)  
            return fail(ec, "write");  
        if(close)  
        {  
            // This means we should close the connection, usually because  
            // the response indicated the "Connection: close" semantic.  
            break;  
        }  
    }  
    // Send a TCP shutdown  
    PROFILE ("shutdown socket",  
    socket.shutdown(tcp::socket::shutdown_send, ec);  
    ); //! PROFILE  
    // At this point the connection is closed gracefully  
}  
```  
  
I'm using [ARC](https://install.advancedrestclient.com/install) as the test client (I tried to write `python` client and `curl` as well, but the results are the same). The request is:  
  
```HTTP  
POST /inference HTTP/1.1  
Host: 143.248.***.***:8080  
Content-Type: image/jpeg  
content-length: 215442  
Connection: keep-alive  
  
[object File]  
```  
The profiling result is:  
  
```BASH  
[::PROFILE] read request: 328.54 ms  
[::PROFILE] handle request: 49.5076 ms  
[::PROFILE] read request: 54.1356 ms  
[::PROFILE] shutdown socket: 0.051028 ms  
```  
  
I think spending `300 ms` just for reading `211 KB` is a bit slow. Both the client and the server are in the same local network with ping < `1 ms`. I tried with `http_server_async.cpp` as well, but it's not improving. Do you have any idea why does this happen and how to improve it?  
Thank you.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-30 06:45:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2042#issuecomment-666161772  

Hi @canhld94 , thanks for reaching out to us.  
  
Without seeing a network packet log (for example from wireshark) I can't be sure, however:  
  
A common source of latency complaints with TCP connections is the fact that Nagle's Algorithm is enabled by default on all newly created sockets. While helpful for reducing network contention with terminal programs that would otherwise send single keystrokes per TCP packet, it can cause unresponsiveness in client/server interactions that deal in bulk data exchange (such as HTTP).  
  
A first port of call would be to disable this algorithm by setting the `SO_NODELAY` option on the socket on both ends.  
  
https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/basic_stream_socket/set_option.html  
  
i.e.  
  
```  
    sock.set_option(asio::ip::tcp::no_delay(true));  
```  
  
If this doesn't help, my next step would to ask to see the packet trace output from wireshark  
https://www.wireshark.org/

---

## Comment 2

> Username: canhld94  
> Created at: 2020-07-30 09:49:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2042#issuecomment-666267112  

Thank you, I will try the solution and report the results.  
  
Get Outlook for iOS<https://aka.ms/o0ukef>  
________________________________  
From: Richard Hodges <notifications@github.com>  
Sent: Thursday, July 30, 2020 3:45:45 PM  
To: boostorg/beast <beast@noreply.github.com>  
Cc: canhld94 <canhld94@gmail.com>; Mention <mention@noreply.github.com>  
Subject: Re: [boostorg/beast] http::read is slow (#2042)  
  
  
Hi @canhld94<https://github.com/canhld94> , thanks for reaching out to us.  
  
Without seeing a network packet log (for example from wireshark) I can't be sure, however:  
  
A common source of latency complaints with TCP connections is the fact that Nagle's Algorithm is enabled by default on all newly created sockets. While helpful for reducing network contention with terminal programs that would otherwise send single keystrokes per TCP packet, it can cause unresponsiveness in client/server interactions that deal in bulk data exchange (such as HTTP).  
  
A first port of call would be to disable this algorithm by setting the SO_NODELAY option on the socket on both ends.  
  
https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/basic_stream_socket/set_option.html  
  
i.e.  
  
    sock.set_option(asio::ip::tcp::no_delay(true));  
  
  
If this doesn't help, my next step would to ask to see the packet trace output from wireshark  
https://www.wireshark.org/  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/beast/issues/2042#issuecomment-666161772>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AFN5VGKAIEBKKKSYDB22TQLR6EJJTANCNFSM4PM5JSUA>.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-07-30 16:49:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2042#issuecomment-666519897  

Reading from the socket SHOULD be the bottleneck for every server, this is normal. You don't need to fiddle with Nagle's algorithm. Are you building with optimizations turned on? How does the performance of your server compare to the performance of Beast's synchronous HTTP server examples compiled with optimizations turned on?

---

## Comment 4

> Username: canhld94  
> Created at: 2020-07-31 01:48:37 UTC  
> Updated at: 2020-07-31 01:49:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2042#issuecomment-666866235  

@madmongo1 I tried the solution and find that it will work when we enable `TCP_NODELAY` or `TCP_LOW_LATENCY` in both client and server. The reading time from the socket is around `6ms` now.    
 @vinniefalco Yes, I agree that reading from the socket should be the bottleneck for every server, however, it's only true if that server serves *human* users, like web servers. In that case, adding a few hundred `ms` to the TTFB won't affect much the UX. However, when we want to build a microservices system where nodes will co-operate to do some tasks and they communicate through TCP, then `latency` does matter and we should optimize it. I build my project with CMake with release configuration, so I guess every optimization flags are turned on. The original Beast's synchronous HTTP server examples do not support `POST` with image body, so comparisons are oranges-to-apples.  
@all Thank you so much for creating a great library. I hope I can go far enough with my project to exploit every feature in the library and learn from it.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-07-31 04:33:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2042#issuecomment-666912884  

Consider using websocket instead, which supports arbitrary-length binary messages.

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-07-31 06:58:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2042#issuecomment-666964946  

Great to see that the problem is solved.  
Closing the issue.
