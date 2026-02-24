# #79 - Missing header in result.hpp [Closed]

> Username: nejati-deriv  
> Created at: 2023-03-07 17:59:47 UTC  
> Updated at: 2023-03-08 11:10:39 UTC  
> Closed at: 2023-03-08 11:10:39 UTC  
> Url: https://github.com/boostorg/redis/issues/79  

It seems `boost/redis/adapter/result.hpp` depends on `boost/redis/error.hpp` but is omitted there.  
```BASH  
boost/redis/adapter/result.hpp:63:15: error: no member named 'error' in namespace 'boost::redis'; did you mean simply 'error'?  
         ec = redis::error::resp3_simple_error;  
```
