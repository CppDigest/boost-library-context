# #751 - How to Read URL of websocket upgrade request [Closed]

> Username: nguoithichkhampha  
> Created at: 2017-08-24 17:54:42 UTC  
> Updated at: 2017-08-25 15:36:54 UTC  
> Closed at: 2017-08-25 15:36:54 UTC  
> Url: https://github.com/boostorg/beast/issues/751  

Hi @vinniefalco  I want to read URL of websocket upgrade request, I should use this api:  
`async_accept_ex(http::header<true,  
        http::basic_fields<Allocator>> const& req,  
            ResponseDecorator const& decorator,  
                AcceptHandler&& handler);`  
that right ?  
and how to construct a http header object ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-24 17:59:09 UTC  
> Url: https://github.com/boostorg/beast/issues/751#issuecomment-324710606  

Hmm, `async_accept_ex` is for when you already have the HTTP upgrade request. If you want to perform custom actions based on the request, you should read the request yourself using `boost::beast::http::read` or `boost::beast::http::async_read`. Call `boost::beast::websocket::is_upgrade` to make sure that it is a valid WebSocket Upgrade request. If that function returns `true` then inspect the `target` field of the request object. Finally, to accept the upgrade request and establish a WebSocket session call `boost::beast::websocket::stream::async_accept_ex` and pass the request object as a parameter.  
  
The documentation provides a description and example for this:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html#beast.using_websocket.handshaking_servers.passing_http_requests  
  
The "advanced" example servers also perform the operation you want:  
  
Line 528:  
https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp#L528

---

## Comment 2

> Username: nguoithichkhampha  
> Created at: 2017-08-25 01:40:09 UTC  
> Url: https://github.com/boostorg/beast/issues/751#issuecomment-324801430  

Oh, I was confused. Thanks for your support. I will try custom it.

---

## Comment 3

> Username: nguoithichkhampha  
> Created at: 2017-08-25 04:51:44 UTC  
> Url: https://github.com/boostorg/beast/issues/751#issuecomment-324823428  

Now I can get websocket upgrade request but don't know how to read url in this request. Do you have document for this case ?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-08-25 12:02:55 UTC  
> Url: https://github.com/boostorg/beast/issues/751#issuecomment-324899437  

Call `target()` on the message to retrieve the URL.

---

## Comment 5

> Username: nguoithichkhampha  
> Created at: 2017-08-25 15:36:54 UTC  
> Url: https://github.com/boostorg/beast/issues/751#issuecomment-324956682  

yeah, I got it. Many thanks.
