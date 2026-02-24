# #217 212 async exec never returns when the network is disconnected and then restored [Merged]

> Username: mzimbres  
> Created at: 2024-10-19 11:08:18 UTC  
> Updated at: 2024-10-25 20:52:45 UTC  
> Merged at: 2024-10-21 20:12:48 UTC  
> Closed at: 2024-10-21 20:12:49 UTC  
> Url: https://github.com/boostorg/redis/pull/217  



---

## Review 1 [Commented]

> Username: bruno-viva  
> Created_at: 2024-10-25 20:52:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/217#pullrequestreview-2396399973  

📁 include/boost/redis/detail/resolver.hpp

```diff
  43 |-          // TODO: map operation_canceled into error::resolve_timeout
  43 |+          if (ec == asio::error::operation_aborted) {
  44 |+             ec == error::resolve_timeout;
```

> Username: bruno-viva  
> Created_at: 2024-10-25 20:52:45 UTC  
> Url: https://github.com/boostorg/redis/pull/217#discussion_r1817334198  

Should this be `ec = error::resolve_timeout`?


---
