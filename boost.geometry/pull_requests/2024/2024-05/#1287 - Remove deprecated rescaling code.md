# #1287 Remove deprecated rescaling code [Merged]

> Username: vissarion  
> Created at: 2024-05-29 13:04:51 UTC  
> Updated at: 2024-08-14 12:07:38 UTC  
> Merged at: 2024-07-08 13:58:36 UTC  
> Closed at: 2024-07-08 13:58:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1287  

This PR removes most (if not all) deprecated code related to rescaling.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-06-02 21:13:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1287#pullrequestreview-2092607251  

📁 include/boost/geometry/core/config.hpp

```diff
  34 |- #if defined(BOOST_GEOMETRY_USE_RESCALING) && defined(BOOST_GEOMETRY_NO_ROBUSTNESS)
  35 |-     #error "Define either BOOST_GEOMETRY_NO_ROBUSTNESS (default) or BOOST_GEOMETRY_USE_RESCALING"
  36 |- #endif
```

> Username: barendgehrels  
> Created_at: 2024-06-02 21:13:25 UTC  
> Updated_at: 2024-06-02 21:13:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1287#discussion_r1623653699  

👍


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2024-06-02 21:17:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1287#pullrequestreview-2092607706  

Great work!!! A milestone!!!  
  
I scanned through much of it (but not every line), it looks good to me.  
  
Thanks a lot.

---
