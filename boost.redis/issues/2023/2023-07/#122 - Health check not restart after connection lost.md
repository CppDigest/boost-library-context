# #122 - Health check not restart after connection lost [Closed]

> Username: ysc3839  
> Created at: 2023-07-24 08:24:17 UTC  
> Updated at: 2023-08-05 21:04:51 UTC  
> Closed at: 2023-08-05 21:04:51 UTC  
> Url: https://github.com/boostorg/redis/issues/122  

In cpp20_subscriber, the health checker periodically logs `Bytes written: 32`. But after `CLIENT kill`, the log disappear.  
Personally guess it's because `checker_has_exited_` is not reset to false:  
https://github.com/boostorg/redis/blob/46525371b9247a1ec208a6157880ec77f5ddadc4/include/boost/redis/detail/health_checker.hpp#L39-L42

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-07-24 21:53:38 UTC  
> Url: https://github.com/boostorg/redis/issues/122#issuecomment-1648678769  

Thanks for reporting, My time is a bit constrained these days. I will have a look at it in the next weekend.
