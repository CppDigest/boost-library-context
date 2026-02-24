# #2713 - Incorrect websocket::stream::async_ping/pong handler requirement in documentation [Closed]

> Username: ecorm  
> Created at: 2023-08-09 21:41:14 UTC  
> Updated at: 2023-08-18 10:45:40 UTC  
> Closed at: 2023-08-18 10:45:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2713  

Beast Version: 347  
  
The [documentation for `websocket::stream::async_ping`](https://www.boost.org/doc/libs/1_82_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_ping.html) incorrectly specifies the _WriteHandler_ requirement, which takes an error code and size parameters. However, the documentation below shows that the handler should only take an error code parameter.  
  
The same goes for `websocket::stream::async_pong`.
