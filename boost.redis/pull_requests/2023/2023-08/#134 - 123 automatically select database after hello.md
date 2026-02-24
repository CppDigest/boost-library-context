# #134 123 automatically select database after hello [Merged]

> Username: mzimbres  
> Created at: 2023-08-06 08:29:29 UTC  
> Updated at: 2023-09-02 12:54:22 UTC  
> Merged at: 2023-08-06 09:00:34 UTC  
> Closed at: 2023-08-06 09:00:34 UTC  
> Url: https://github.com/boostorg/redis/pull/134  



---

## Review 1 [Commented]

> Username: ysc3839  
> Created_at: 2023-08-07 02:32:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/134#pullrequestreview-1564465996  

📁 include/boost/redis/config.hpp

```diff
  50 |    std::string clientname = "Boost.Redis";
  51 | 
  52 |+    /// Database that will be passed to the [SELECT](https://redis.io/commands/hello/) command.
```

> Username: ysc3839  
> Created_at: 2023-08-07 02:32:25 UTC  
> Updated_at: 2023-08-07 02:32:26 UTC  
> Url: https://github.com/boostorg/redis/pull/134#discussion_r1285326191  

This link is wrong.


---
