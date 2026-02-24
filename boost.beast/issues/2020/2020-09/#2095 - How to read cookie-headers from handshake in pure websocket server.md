# #2095 - How to read cookie/headers from handshake in pure websocket server? [Closed]

> Username: yuntonyx  
> Created at: 2020-09-18 22:39:56 UTC  
> Updated at: 2020-09-19 03:11:38 UTC  
> Closed at: 2020-09-19 03:11:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2095  

Hey all,  
  
I am trying to read cookies from the handshake http request in this example:   
https://www.boost.org/doc/libs/1_74_0/libs/beast/example/websocket/server/async-ssl/websocket_server_async_ssl.cpp  
  
I tried the following:  
```  
boost::beast::http::request<boost::beast::http::empty_body> req;  
ws_.async_accept(  
            req,  
            beast::bind_front_handler(  
                &session::on_accept,  
                shared_from_this()));  
```  
  
but after changing the code, I am getting the error `The WebSocket handshake method was not GET`  
  
Is it possible to read header values / cookies without making the server dual protocols?  
  
Thank you

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-18 22:51:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2095#issuecomment-695118824  

You need to use overload 3 of websocket::stream::async_accept.  
  
https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html  
  
You pass it a reference to the originating request so it can continue the upgrade rather than waiting for a new GET request.

---

## Comment 2

> Username: yuntonyx  
> Created at: 2020-09-19 01:35:01 UTC  
> Updated at: 2020-09-19 01:36:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2095#issuecomment-695146277  

Where can I get the originating request? In my first post I did try to use overload 3 of async_accept, but now I see the issue that async_accept needs the originating request instead of a new one.  
  
right now in the example overload 1 of websocket::stream::async_accept is called in on_handshake, and on_handshake is called through   
```  
ws_.next_layer().async_handshake(  
            ssl::stream_base::server,  
            beast::bind_front_handler(  
                &session::on_handshake,  
                shared_from_this()));  
```  
  
After reading both documentations on async_handshake and async_accept, I don't see a way to get a `boost::beast::http::request`? Can you please point me in the right direction?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-19 02:38:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2095#issuecomment-695153178  

> Can you please point me in the right direction?  
  
Yes: https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests

---

## Comment 4

> Username: yuntonyx  
> Created at: 2020-09-19 03:08:10 UTC  
> Updated at: 2020-09-19 03:09:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2095#issuecomment-695155894  

> https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests  
  
~~Thanks! Just to make sure, is this the only way to get the header of the websocket handshake request (by implementing dual protocol)? Because I do not want to handle any other http requests.~~  
  
EDIT: I think I have understood the article. Thank you very much for your help!
