# #2918 - Feature Request: Support for WebSocket over Unix Domain Sockets [Closed]

> Username: zhengfeihe  
> Created at: 2024-08-10 13:43:47 UTC  
> Updated at: 2025-02-28 13:08:38 UTC  
> Closed at: 2025-02-28 13:08:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2918  

Hi Maintainers,  
  
I have found, support for using custom external sockets is not available in beast, as noted in this [issue](https://github.com/boostorg/beast/issues/2055). Specifically, I encountered this limitation when trying to use WebSocket over Unix sockets. This seems to be a missing feature in many C++ WebSocket libraries. There were also issues related to using WebSockets over Unix domain sockets in [websocketpp](https://github.com/zaphoyd/websocketpp/issues/557), but it appears to be unmaintained.  
  
Building WebSocket communication over Unix domain sockets is supported in some packages for other languages. For example, in Python's websocket-client package, you can configure a Unix domain socket when creating client connections: [websocket-client](https://websocket-client.readthedocs.io/en/latest/examples.html#connecting-with-custom-sockets).  
  
Do you think this is a feature that could be added to Beast? If so, would it be acceptable for me to work on this? It may take some time for me to implement.  
  
Best,  
Zhengfei

---

## Comment 1

> Username: ashtum  
> Created at: 2024-08-10 16:02:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2918#issuecomment-2282194750  

You can easily use a different socket type for the underlying stream of `websocket::stream`:  
  
```C++  
using ws_stream_t = beast::websocket::stream<asio::local::stream_protocol::socket>;  
```  
  
The [`websocket::stream::next_layer`](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/next_layer/overload2.html) function gives you access to the underlying stream, allowing you to perform the connect operation. Alternatively, you can move an already connected socket into the websocket stream constructor.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-08-10 16:54:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2918#issuecomment-2282208577  

We should provide a working example program for this since it keeps coming up.

---

## Comment 3

> Username: zhengfeihe  
> Created at: 2024-08-11 15:01:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2918#issuecomment-2282789210  

Thanks for your reply and explanation! I will close the issue.

---

## Comment 4

> Username: savely-krasovsky  
> Created at: 2024-08-17 19:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2918#issuecomment-2294960198  

I would also add an example with Windows Named Pipe. I know, Windows supports UNIX domain sockets since Windows 10, but named pipes has better Windows Security support (you can run named pipe with own security descriptor).

---

## Comment 5

> Username: vinniefalco  
> Created at: 2024-08-17 23:37:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2918#issuecomment-2295027329  

Does asio have a windows named pipe implementation that meets the stream requirements?

---

## Comment 6

> Username: savely-krasovsky  
> Created at: 2024-08-18 02:11:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2918#issuecomment-2295065485  

@vinniefalco yes: https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/overview/windows/stream_handle.html  
I tried it with http client, works perfectly.
