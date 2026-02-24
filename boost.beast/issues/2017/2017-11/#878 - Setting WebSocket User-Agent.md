# #878 - Setting WebSocket User-Agent? [Closed]

> Username: NuSkooler  
> Created at: 2017-11-09 21:14:29 UTC  
> Updated at: 2017-11-10 21:13:50 UTC  
> Closed at: 2017-11-10 21:13:50 UTC  
> Url: https://github.com/boostorg/beast/issues/878  

This is only a question. Is it possible to get the User-Agent when using WebSocket streams (e.g. `websocket::stream<tcp::socket>`)? Am I missing something here?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-10 04:13:52 UTC  
> Updated at: 2017-11-10 04:14:09 UTC  
> Url: https://github.com/boostorg/beast/issues/878#issuecomment-343370283  

You can get the User-Agent as a server by reading in the request yourself, inspecting the field, then calling `stream.accept` or `stream.async_accept` and passing the request. You can set the User-Agent as a client by calling `stream.handshake` or `stream.async_handshake` and providing a "decorator." This is explained in the documentation:  
  
As a **client**:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/handshaking_clients.html#beast.using_websocket.handshaking_clients.decorators  
  
As a **server**:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html#beast.using_websocket.handshaking_servers.passing_http_requests

---

## Comment 2

> Username: NuSkooler  
> Created at: 2017-11-10 21:13:50 UTC  
> Url: https://github.com/boostorg/beast/issues/878#issuecomment-343588031  

@vinniefalco Perfect, thank you!
