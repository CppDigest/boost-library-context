# #69 - Move health-check functionality from examples to the library [Closed]

> Username: mzimbres  
> Created at: 2023-02-19 09:50:27 UTC  
> Updated at: 2023-03-05 20:18:13 UTC  
> Closed at: 2023-03-05 20:18:13 UTC  
> Url: https://github.com/boostorg/redis/issues/69  

Healthy checks are a fundamental part of any library that communicates with the database. Boost.Redis shows how to write one in the examples but it is based on coroutines. Given that every serious user will need this functionality, we should provide it a library function. I think it can use the following signature.  
  
```cpp  
auto async_health_check(connection, request, interval, token);  
```  
  
This function would then remain suspend for as long as Redis is alive and exit without error when the request (ping) times-out. Given that it is generic in regard to what request is sent it could also be named `async_repeat`.
