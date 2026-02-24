# #2829 - Question: Can Beast run on top of Unix domain sockets (aka `local::stream_protocol`)? [Closed]

> Username: Alexander-Shukaev  
> Created at: 2024-02-26 13:12:34 UTC  
> Updated at: 2024-04-04 08:00:04 UTC  
> Closed at: 2024-04-04 08:00:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2829  

Can Beast run on top of Unix domain sockets (aka `local::stream_protocol`)?  
  
Thanks.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-02-26 13:38:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2829#issuecomment-1964170733  

You can pass a different protocol to [beast::basic_stream](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream.html), for example:  
```C++  
using stream_t = beast::basic_stream<asio::local::stream_protocol>;  
```

---

## Comment 2

> Username: Alexander-Shukaev  
> Created at: 2024-02-26 13:51:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2829#issuecomment-1964196483  

So does this imply that TCP is not essential for Beast framework (HTTP/websocket in general)?  I am a bit surprised here.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-02-26 14:21:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2829#issuecomment-1964261294  

No, there is no coupling to TCP streams, as long as the underlying stream meets the requirements, it can be used for HTTP and WebSocket operations: https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/concepts/streams.html

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-02-27 17:05:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2829#issuecomment-1967168127  

TCP is not essential, but you will need to implement the `teardown` and `async_teardown` customization points for your stream type (Unix domain sockets in this case) when using beast websockets.
