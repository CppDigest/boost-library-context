# #1822 - Too much strand [Closed]

> Username: vinniefalco  
> Created at: 2020-01-25 14:18:17 UTC  
> Updated at: 2020-01-28 16:48:34 UTC  
> Closed at: 2020-01-28 16:48:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1822  

There's no need for 2 separate strands (I think), these should use the same one:  
https://github.com/boostorg/beast/blob/develop/example/http/client/async-ssl/http_client_async_ssl.cpp#L58  
  
In other words it should read (I think):  
```  
    explicit  
    session(net::io_context& ioc)  
        : resolver_(net::make_strand(ioc))  
        , stream_(resolver_.get_executor())  
    {  
    }  
```  
  
We need to check every call to `make_strand` in the examples and ensure they are correct.
