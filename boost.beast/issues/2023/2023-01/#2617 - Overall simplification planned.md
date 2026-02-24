# #2617 - Overall simplification planned? [Closed]

> Username: ghost  
> Created at: 2023-01-10 14:37:40 UTC  
> Updated at: 2023-01-10 15:44:20 UTC  
> Closed at: 2023-01-10 15:44:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2617  

Hi,  
I was reading the documentation for Beast and for another library [(uWS](https://github.com/uNetworking/uWebSockets#battery-batteries-included), not to name it).  
  
Beast looks very powerful but it seems many details can be forgotten while using it.  
for example [this link,](https://www.boost.org/doc/libs/1_81_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp) which looks like a bare bone, has:  
  
```  
http::async_write(stream_, req_,  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
```  
That's a lot of typing. I get the inherent nature of async, but some typing should be removed.  
  
Could it be possible to have something like:  
  
http::async_write(stream_, req_, [](/*arguments*/){  
    /* do things...*/        
});  
  
Overall I find Beast very verbose and error prone, (maybe leading to miss some performance optimisations).  
Is there a way to make it less so?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-01-10 14:39:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2617#issuecomment-1377378744  

Yes, by using `asio::awaitable` & C++20 coroutines. Examples are included.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-01-10 14:40:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2617#issuecomment-1377380512  

> Overall I find Beast very verbose and error prone  
  
Beast is based on Asio so it inherits the same syntax and idioms.

---

## Comment 3

> Username: ghost  
> Created at: 2023-01-10 15:02:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2617#issuecomment-1377408466  

Could an overload be possible then:  
  
```  
    // This buffer is used for reading and must be persisted  
    beast::flat_buffer b;  
  
    // Declare a container to hold the response  
    http::response<http::dynamic_body> res;  
  
    // Receive the HTTP response  
    co_await http::async_read(stream, b, res);  
```  
  
to   
  
  
```  
    // Declare a container to hold the response  
    http::response<http::dynamic_body> res;  
  
    // Receive the HTTP response  
    co_await http::async_read(stream, res);  
```  
and have the overload declare the flat_buffer by itself ?  
Less typing.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-01-10 15:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2617#issuecomment-1377461340  

No. "must be persisted"

---

## Comment 5

> Username: ghost  
> Created at: 2023-01-10 15:44:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2617#issuecomment-1377466474  

Ok, "pass".
