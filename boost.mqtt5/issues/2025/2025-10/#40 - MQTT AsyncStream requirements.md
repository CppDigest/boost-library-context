# #40 - MQTT AsyncStream requirements? [Closed]

> Username: vinniefalco  
> Created at: 2025-10-24 23:02:33 UTC  
> Updated at: 2025-10-28 12:09:33 UTC  
> Closed at: 2025-10-28 12:09:26 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/40  

The documentation states that `boost::beast::websocket::stream` satisfies _AsyncWriteStream_ yet this is not possible, because Beast's `async_write_some` requires a boolean parameter:  
  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write_some.html

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-10-28 12:09:33 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/40#issuecomment-3456156034  

Thanks
