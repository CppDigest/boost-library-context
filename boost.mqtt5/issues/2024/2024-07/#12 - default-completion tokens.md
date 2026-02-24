# #12 - default-completion tokens [Closed]

> Username: klemens-morgenstern  
> Created at: 2024-07-27 00:52:44 UTC  
> Updated at: 2024-08-09 07:45:40 UTC  
> Closed at: 2024-08-09 07:45:40 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/12  

With boost 1.86, there is a [global default completion token](https://github.com/boostorg/asio/commit/6ce241c2bb4217c1f22f0b25f70af637dcf27e37), so this library should add support.   
  
Changing this  
  
```cpp  
template<typename CompletionToken>   
auto async_foo( ..., CompletionToken && token)  
```  
  
to   
  
```cpp  
template<typename CompletionToken = typename ::boost::asio::default_completion_token<executor_type>::type>   
auto async_foo(..., CompletionToken && token = typename ::boost::asio::default_completion_token<executor_type>::type())  
```  
  
should do that and will allow code like this:  
  
```cpp  
  
co_await async_foo(...); // not token needed, default is co_await-able   
```

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-08-09 07:45:40 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/12#issuecomment-2277358382  

Resolved with 7bc1ccf072edd1c87e6c343383c27606d83e9be3.
