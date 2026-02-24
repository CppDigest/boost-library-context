# #1976 - Implement SSE (server Side Event) Server [Closed]

> Username: neel  
> Created at: 2020-06-07 17:36:07 UTC  
> Updated at: 2020-06-08 08:48:50 UTC  
> Closed at: 2020-06-08 08:48:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1976  

I am trying to implement SSE using beast. But all the overloads on write_async() and write_some_async() takes an http message object. But I need to write raw strings on the Socket. How can I do that ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-06-07 18:07:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1976#issuecomment-640257144  

Use Asio to write raw octets to the socket.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-06-08 08:48:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1976#issuecomment-640463230  

https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/async_write.html
