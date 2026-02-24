# #1385 - how to get headers in the websocket server? [Closed]

> Username: kylejan  
> Created at: 2018-12-22 07:14:51 UTC  
> Updated at: 2018-12-24 01:55:59 UTC  
> Closed at: 2018-12-24 01:55:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1385  

how to get headers in the websocket server?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-22 08:15:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449554251  

The docs explain it

---

## Comment 2

> Username: kylejan  
> Created at: 2018-12-23 01:56:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449608980  

cannot find the corresponding doc page, could you please post the link? thank u!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-23 02:21:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449609779  

For clients:  
  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_websocket/handshaking_clients.html#beast.using_websocket.handshaking_clients.filtering  
  
For servers:  
  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html#beast.using_websocket.handshaking_servers.passing_http_requests  
  
The advanced servers demonstrate how to receive the websocket HTTP Upgrade request:  
  
https://www.boost.org/doc/libs/1_69_0/libs/beast/example/advanced/server/advanced_server.cpp  
  
Here:  
https://github.com/boostorg/beast/blob/f00237cb35bcb0d3d544db4b80de38f1aba78ab0/example/advanced/server/advanced_server.cpp#L664

---

## Comment 4

> Username: kylejan  
> Created at: 2018-12-23 09:13:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449623831  

it seems that i have to set up a http server before websocket session, is that right?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-12-23 12:55:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449634650  

You don't need an "HTTP server" you just need to call `http::async_read` to read the request, inspect the headers, before passing it to `websocket::stream::async_accept`.

---

## Comment 6

> Username: kylejan  
> Created at: 2018-12-23 16:09:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449646240  

thank u! it works on server without ssl now. is that i have to use http ssl::stream to do the ssl handshake before http::async_read in the case of async_ssl_websocket_server?

---

## Comment 7

> Username: kylejan  
> Created at: 2018-12-23 16:11:59 UTC  
> Updated at: 2018-12-23 16:45:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449646388  

how to do the websocket ssl handshake after the previous procedures? i try to call ws_.next_layer().async_handshake in http on_read callback, but the ws handshake callback will not be called.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-12-23 16:53:04 UTC  
> Updated at: 2018-12-23 16:53:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449649654  

> The advanced servers demonstrate how to receive the websocket HTTP Upgrade request:  
  
https://github.com/boostorg/beast/blob/f00237cb35bcb0d3d544db4b80de38f1aba78ab0/example/advanced/server/advanced_server.cpp#L263  
  
Use this overload of `websocket::stream::async_accept`  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html

---

## Comment 9

> Username: kylejan  
> Created at: 2018-12-24 01:41:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449675437  

i had tried it, the error code will be "uninitialized".

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-12-24 01:46:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449675670  

Have you compiled and run the advanced server? Does it work for you?

---

## Comment 11

> Username: kylejan  
> Created at: 2018-12-24 01:50:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1385#issuecomment-449675901  

oh i know why i get this error code, because i am not use the same ssl::stream layer in http read and websocket accept. now it works, thanks a lot!
