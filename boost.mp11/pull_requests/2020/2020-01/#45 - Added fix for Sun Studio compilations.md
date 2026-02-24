# #45 Added fix for Sun Studio compilations. [Merged]

> Username: vahtis  
> Created at: 2020-01-28 13:41:06 UTC  
> Updated at: 2020-01-28 23:28:18 UTC  
> Merged at: 2020-01-28 23:28:18 UTC  
> Closed at: 2020-01-28 23:28:18 UTC  
> Url: https://github.com/boostorg/mp11/pull/45  

Added define so I was able to build using Studio 12.6 Sun C++

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2020-01-28 16:24:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/45#pullrequestreview-349514493  

📁 include/boost/mp11/detail/config.hpp

```diff
 133 | // -pedantic warns about [[deprecated]] when in C++11 mode
 134 | #  define BOOST_MP11_DEPRECATED(msg) __attribute__((deprecated(msg)))
 135 |+ #elif __SUNPRO_CC
```

> Username: pdimov  
> Created_at: 2020-01-28 16:24:02 UTC  
> Updated_at: 2020-01-28 18:28:52 UTC  
> Url: https://github.com/boostorg/mp11/pull/45#discussion_r371909821  

Thanks. Should be `#elif defined(__SUNPRO_CC)` though.

> Username: vahtis  
> Created_at: 2020-01-28 18:30:21 UTC  
> Updated_at: 2020-01-28 18:30:22 UTC  
> Url: https://github.com/boostorg/mp11/pull/45#discussion_r371980259  

Changed it


---
