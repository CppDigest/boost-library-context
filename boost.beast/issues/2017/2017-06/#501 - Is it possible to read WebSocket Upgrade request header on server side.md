# #501 - Is it possible to read WebSocket Upgrade request header on server side? [Closed]

> Username: onto  
> Created at: 2017-06-16 13:50:33 UTC  
> Updated at: 2017-06-16 15:29:14 UTC  
> Closed at: 2017-06-16 15:26:42 UTC  
> Url: https://github.com/boostorg/beast/issues/501  

I need to check Origin field from request header (filter websocket clients on server somewhere on `websocket::stream::async_accept` call), but it seems there no way to it now. Is it?

---

## Comment 1

> Username: hoditohod  
> Created at: 2017-06-16 13:56:22 UTC  
> Url: https://github.com/boostorg/beast/issues/501#issuecomment-309032591  

When you accept a connection you first read a plain http request and then check if it's an upgrade request, if it is, then start a websocket handshake. You can access all the headers from the first http request.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-16 14:19:29 UTC  
> Url: https://github.com/boostorg/beast/issues/501#issuecomment-309038372  

Covered in the docs :)  
http://vinniefalco.github.io/beast/beast/websocket/server.html

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-16 14:48:12 UTC  
> Url: https://github.com/boostorg/beast/issues/501#issuecomment-309046050  

Use this overload  
http://vinniefalco.github.io/beast/beast/ref/beast__websocket__stream/async_accept/overload4.html

---

## Comment 4

> Username: onto  
> Created at: 2017-06-16 15:26:42 UTC  
> Url: https://github.com/boostorg/beast/issues/501#issuecomment-309056469  

Thanks, it works. 👍

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-16 15:29:14 UTC  
> Url: https://github.com/boostorg/beast/issues/501#issuecomment-309057166  

Another satisfied customer, cha-ching!!
