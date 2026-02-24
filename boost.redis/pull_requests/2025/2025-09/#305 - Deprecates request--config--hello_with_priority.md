# #305 Deprecates request::config::hello_with_priority [Merged]

> Username: anarthal  
> Created at: 2025-09-15 12:35:54 UTC  
> Updated at: 2025-09-15 22:49:38 UTC  
> Merged at: 2025-09-15 22:49:33 UTC  
> Closed at: 2025-09-15 22:49:33 UTC  
> Url: https://github.com/boostorg/redis/pull/305  



---

## Review 1 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-15 20:19:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/305#pullrequestreview-3226145495  

📁 include/boost/redis/request.hpp

```diff
  80 |+        * This field will be removed in subsequent releases.
  81 |        */
  82 |       bool hello_with_priority = true;
```

> Username: mzimbres  
> Created_at: 2025-09-15 20:19:00 UTC  
> Url: https://github.com/boostorg/redis/pull/305#discussion_r2350011039  

I have to ask again: We are deprecating but it won't be removed, just renamed to e.g. has_priority and we will hide it from users?

> Username: anarthal  
> Created_at: 2025-09-15 20:30:39 UTC  
> Updated_at: 2025-09-15 20:30:40 UTC  
> Url: https://github.com/boostorg/redis/pull/305#discussion_r2350050238  

Yes. My plan is keeping the member (has_priority_) and the request_access members as we have them today. So only hiding it.


---
