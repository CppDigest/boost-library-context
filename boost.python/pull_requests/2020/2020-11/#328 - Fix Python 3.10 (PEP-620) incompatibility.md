# #328 Fix Python 3.10 (PEP-620) incompatibility. [Merged]

> Username: stefanseefeld  
> Created at: 2020-11-14 17:40:16 UTC  
> Updated at: 2021-04-26 18:49:54 UTC  
> Merged at: 2021-01-04 06:31:41 UTC  
> Closed at: 2021-01-04 06:31:41 UTC  
> Url: https://github.com/boostorg/python/pull/328  



---

## Review 1 [Commented]

> Username: vstinner  
> Created_at: 2020-12-16 11:39:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/328#pullrequestreview-553603669  

📁 include/boost/python/detail/wrap_python.hpp

```diff
 232 |+ #if PY_VERSION_HEX < 0x030900A4
 233 |+ #  define Py_SET_TYPE(obj, type) ((Py_TYPE(obj) = (type)), (void)0)
 234 |+ #  define Py_SET_SIZE(obj, size) ((Py_SIZE(obj) = (size)), (void)0)
```

> Username: vstinner  
> Created_at: 2020-12-16 11:39:22 UTC  
> Updated_at: 2021-01-04 05:23:19 UTC  
> Url: https://github.com/boostorg/python/pull/328#discussion_r544229176  

I wrote PR #330 which uses pythoncapi_compat.h to not have to define these compatibility macros manually.


---
