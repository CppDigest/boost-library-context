# #318 - async_read cannot be used with use_awaitable [Closed]

> Username: andrei-datcu  
> Created at: 2020-01-18 01:50:52 UTC  
> Updated at: 2020-12-30 01:12:28 UTC  
> Closed at: 2020-12-30 01:12:27 UTC  
> Url: https://github.com/boostorg/asio/issues/318  

```  
    co_await boost::asio::async_read(socket, aBuffer, boost::asio::use_awaitable);  
```  
fails to compile with:  
```  
Error	C2039	'completion_handler_type': is not a member of 'boost::asio::async_result<boost::asio::use_awaitable_t<boost::asio::executor>,Signature>'  
```  
  
What's the future plan here? Will default completion token support this? `async_read_until` doesn't exhibit this issue as it doesn't do any readhandler validation. Any particular reason `async_read` is the only one doing it?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:12:26 UTC  
> Url: https://github.com/boostorg/asio/issues/318#issuecomment-752293359  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#741](https://github.com/chriskohlhoff/asio/issues/741).
