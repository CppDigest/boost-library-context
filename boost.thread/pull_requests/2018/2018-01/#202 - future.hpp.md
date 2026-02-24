# #202 future.hpp: [Merged]

> Username: shinobu-x  
> Created at: 2018-01-22 02:08:29 UTC  
> Updated at: 2018-01-22 22:37:59 UTC  
> Merged at: 2018-01-22 22:37:58 UTC  
> Closed at: 2018-01-22 22:37:58 UTC  
> Url: https://github.com/boostorg/thread/pull/202  

1) Removed redundant access specifiers and unused variables in future_deferred_shared_state  
2) check lock before unlock

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2018-01-22 18:09:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/202#pullrequestreview-90570040  

📁 include/boost/thread/future.hpp

```diff
1128 |                     {
1136 |-                         locks[i].unlock();
1129 |+                         if (locks[i].owns_lock())
```

> Username: viboes  
> Created_at: 2018-01-22 18:09:27 UTC  
> Updated_at: 2018-01-22 19:58:46 UTC  
> Url: https://github.com/boostorg/thread/pull/202#discussion_r163020344  

Hi, have you found a case where the lock where not locked?

> Username: shinobu-x  
> Created_at: 2018-01-22 19:56:29 UTC  
> Updated_at: 2018-01-22 19:58:46 UTC  
> Url: https://github.com/boostorg/thread/pull/202#discussion_r163051196  

@viboes I've completely missed this function is expected to `unlock`


---
