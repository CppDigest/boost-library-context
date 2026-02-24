# #319 - Use flat_streambuf in websocket::stream::stream_ [Closed]

> Username: vinniefalco  
> Created at: 2017-04-25 02:42:53 UTC  
> Updated at: 2017-08-06 22:42:52 UTC  
> Closed at: 2017-08-06 22:42:52 UTC  
> Url: https://github.com/boostorg/beast/issues/319  

To avoid an allocation

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-06 22:42:52 UTC  
> Url: https://github.com/boostorg/beast/issues/319#issuecomment-320537699  

websocket stream uses `static_buffer` for reads now.
