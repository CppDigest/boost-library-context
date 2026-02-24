# #2969 - websocket::stream::async_read_some docs should mention how to identify message completion [Closed]

> Username: ashtum  
> Created at: 2025-01-08 13:23:00 UTC  
> Updated at: 2025-02-11 09:11:32 UTC  
> Closed at: 2025-02-11 09:11:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2969  

The [websocket::stream::async_read_some](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read_some.html) page should mention how [websocket::stream::is_message_done](https://www.boost.org/doc/libs/1_87_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/is_message_done.html) can be used to determine if the last read operation completed the current message.
