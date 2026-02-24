# #104 Support additional is_empty intrinsics [Merged]

> Username: glenfe  
> Created at: 2021-12-07 16:01:47 UTC  
> Updated at: 2021-12-07 19:21:38 UTC  
> Merged at: 2021-12-07 16:02:05 UTC  
> Closed at: 2021-12-07 16:02:05 UTC  
> Url: https://github.com/boostorg/core/pull/104  



---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2021-12-07 19:21:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/104#pullrequestreview-825687740  

📁 include/boost/core/allocator_access.hpp

```diff
  22 | #if defined(BOOST_GCC_VERSION) && (BOOST_GCC_VERSION >= 40300)
  23 |- #define BOOST_DETAIL_ALLOC_HAS_IS_EMPTY
  23 |+ #define BOOST_DETAIL_ALLOC_EMPTY(T) __is_empty(T)
```

> Username: Lastique  
> Created_at: 2021-12-07 19:20:40 UTC  
> Updated_at: 2021-12-07 19:21:38 UTC  
> Url: https://github.com/boostorg/core/pull/104#discussion_r764294965  

Perhaps `BOOST_DETAIL_ALLOC_IS_EMPTY` (note the added `_IS`)?


---
