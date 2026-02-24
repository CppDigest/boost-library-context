# #342 Adds request::append() [Merged]

> Username: anarthal  
> Created at: 2025-10-26 13:24:58 UTC  
> Updated at: 2025-10-27 14:18:49 UTC  
> Merged at: 2025-10-27 14:18:46 UTC  
> Closed at: 2025-10-27 14:18:46 UTC  
> Url: https://github.com/boostorg/redis/pull/342  

close #341

---

## Review 1 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-26 20:38:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/342#pullrequestreview-3381432201  

📁 include/boost/redis/request.hpp

```diff
 353 |+       commands_ += other.commands_;
 354 |+       expected_responses_ += other.expected_responses_;
 355 |+    }
```

> Username: mzimbres  
> Created_at: 2025-10-26 20:38:32 UTC  
> Updated_at: 2025-10-26 20:38:33 UTC  
> Url: https://github.com/boostorg/redis/pull/342#discussion_r2464101236  

Move to request.ipp.


---
