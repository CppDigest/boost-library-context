# #1762 - Help with performance drop at certain websocket data sizes [Closed]

> Username: emmenlau  
> Created at: 2019-11-09 21:13:53 UTC  
> Updated at: 2019-11-09 22:48:03 UTC  
> Closed at: 2019-11-09 22:31:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1762  

This is more of a question that an actual issue report. Please feel free to ignore!  
  
I've slightly modified the awesome [advanced_server_flex.cpp](https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp) to create a C++ Apache thrift websocket server that I'm planning to open-source. I haven't managed to clean up the code and its admittedly still pretty basic. However everything works and I'm quite impressed of boost beast! Two thumbs up!  
  
A first performance benchmark from Google Chrome with Apache thrift nodejs implementation shows a promising data transfer from the websocket server to a Javascript browser client. Except there is a reproducible, very strange drop of performance for data sizes around 10 to 100 KByte. The actual numbers I get are given in below table:  
```  
RPC Data Size (Byte) | Calls/sec | Throughput (Byte/sec)  
                   1 |   6145.89 |       6145.89   
                  10 |   5973.96 |      59739.61  
                 100 |   5806.32 |     580632.44  
                1000 |   4973.85 |    4973853.86  
               10000 |     22.12 |     221187.28  
              100000 |     21.90 |    2189918.49  
             1000000 |     48.09 |   48089451.66  
            10000000 |      4.41 |   44112357.20  
```  
As can be seen, there is a "strange" but reproducible drop in calls/sec and throughput for data sizes of 10K and 100K. Even more surprising is the throughput picks up speed later again!  
  
I understand that this issue report is not very telling. And admittedly there are quite a number of components involved in this software stack: boost beast, Apache thrift in C++ and nodejs, and last not least Google Chrome.  
  
But I'm merely hoping for a pointer in which direction to look for the problem, as admittedly I'm quite new to most of these technologies. Is there any known knob in beast that may coincide with my measurements? For example an internal buffer or something? Anything I can do to collect more information?  
  
### Version of Beast  
```  
#define BOOST_BEAST_VERSION 266  
```  
  
### Steps necessary to reproduce the problem  
  
I'm using a mostly unmodified version of the advanced_server_flex.cpp, where in `websocket_session::on_read()` I reserve a `buffer_` size of 11MB (the maximum message size I benchmark), copy the bytes of the thrift message, and `commit()` the buffer size of the actual message I want to send.  
  
### All relevant compiler information  
  
```  
gcc (Ubuntu 8.3.0-6ubuntu1) 8.3.0 for x86_64 with -O2  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-09 21:34:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1762#issuecomment-552139822  

There is a small buffer used to coalesce reads here:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/stream_impl.hpp#L84  
  
But if the incoming message is larger than the buffer, it skips the buffer and reads directly into the caller's mutable buffer sequence.  
  
To start, are you sending or receiving? How big are the messages going into beast, and how big are the messages going out? Do you have compression turned on? Do you have the auto-fragment outgoing frames turned on?

---

## Comment 2

> Username: emmenlau  
> Created at: 2019-11-09 22:06:29 UTC  
> Updated at: 2019-11-09 22:20:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1762#issuecomment-552141967  

Thanks for the quick response!  
  
The benchmark is exclusively for sending from beast to client. The exchange is triggered by an RPC-request from the thrift client coming into the server, but the request message is typically smaller than a few hundred bytes. The server responds to the RPC call with a message that is made up of a small header and a huge binary blob payload. The message sizes are the size given in the benchmark table above (currently byte sizes of powers of 10, from 0 to 7).  
  
My server is an almost unmodified [advanced_server_flex.cpp](https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp) with binary transfer mode. I only added the thrift server data exchange on top of `websocket_session::on_read()`, but my changes in fact are really not doing much more than copying the binary RPC response into the `flat_buffer` of the websocket and calling `async_write()`.  
  
As far as I understand the advanced_server_flex.cpp does not have compression enabled, and I did not change anything related to auto-fragment outgoing frames. Is that good or bad?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-09 22:20:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1762#issuecomment-552142972  

Try turning auto-fragment off:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/auto_fragment/overload1.html

---

## Comment 4

> Username: emmenlau  
> Created at: 2019-11-09 22:31:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1762#issuecomment-552143716  

Awesome! This was exactly the issue! Now I get consistent throughput for all tested sizes! Thanks a lot @vinniefalco .
