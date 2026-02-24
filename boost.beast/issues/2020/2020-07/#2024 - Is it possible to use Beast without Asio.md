# #2024 - Is it possible to use Beast without Asio? [Closed]

> Username: ghost  
> Created at: 2020-07-19 00:47:30 UTC  
> Updated at: 2020-07-19 08:18:25 UTC  
> Closed at: 2020-07-19 08:18:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2024  

Can I do the I/O myself and communicate with Beast using memory buffers?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-07-19 01:39:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2024#issuecomment-660570564  

For HTTP, yes. See:  
https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/using_http/buffer_oriented_serializing.html  
https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/using_http/buffer_oriented_parsing.html  
  
For Websocket, no.
