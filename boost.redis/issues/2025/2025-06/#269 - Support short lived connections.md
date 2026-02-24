# #269 - Support short lived connections [Closed]

> Username: mzimbres  
> Created at: 2025-06-10 11:24:20 UTC  
> Updated at: 2025-11-19 21:31:20 UTC  
> Closed at: 2025-11-19 21:31:20 UTC  
> Url: https://github.com/boostorg/redis/issues/269  

To implement sentinel based failover we need better support for short lived connections. At the moment there is no way to launch `async_exec` and `async_run` in parallel and cancel when `async_exec` completes, see [this](https://github.com/boostorg/redis/blob/develop/example/cpp20_resolve_with_sentinel.cpp#L44-L46) comment.   
  
Also, short lived connections don't need health-check but disabling them [here](https://github.com/boostorg/redis/blob/develop/include/boost/redis/config.hpp#L72) does not prevent their operations from being started [here](https://github.com/boostorg/redis/blob/develop/include/boost/redis/connection.hpp#L372-L377) which should be avoided if possible to improve compilation times.  
  
Moreover, for should lived connections it should be possible to send both the `HELLO` command and the actual request in a single request object, this is currently not possible because the resp3-handshake can't be deactivated [here](https://github.com/boostorg/redis/blob/develop/include/boost/redis/connection.hpp#L370).  
  
We might be able to address these two points by adding an `async_run_exec` function that only launches the request and the read and write operations.

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-06-10 13:40:39 UTC  
> Url: https://github.com/boostorg/redis/issues/269#issuecomment-2959309795  

I think it would be just simpler to avoid using the connection for short-lived connections and instead add a free function  
  
```cpp  
auto async_exec(request const&, response&, config, CompletionToken);  
```  
  
This function would then dynamically allocate a `redis_stream`.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-06-10 14:07:51 UTC  
> Url: https://github.com/boostorg/redis/issues/269#issuecomment-2959407703  

Are you envisioning this as a detail function or as a public one?

---

## Comment 3

> Username: mzimbres  
> Created at: 2025-06-10 22:02:55 UTC  
> Url: https://github.com/boostorg/redis/issues/269#issuecomment-2960684400  

A function in `detail`, I don't see any other usage for this except for failover with sentinels.  
  
A small correction: given that multiple sentinels might be tried before finding one that is responsive, reusing the `redis_stream` would result in lower overhead and therefore lower latency, perhaps unmeasurable but still. So, the function signature would then have to be  
```cpp  
auto async_exec(redis_stream&, request const&, response&, config, CompletionToken);  
```
