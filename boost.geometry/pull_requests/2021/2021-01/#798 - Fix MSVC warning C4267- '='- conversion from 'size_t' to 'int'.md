# #798 Fix MSVC warning C4267: '=': conversion from 'size_t' to 'int' [Merged]

> Username: mloskot  
> Created at: 2021-01-20 22:40:47 UTC  
> Updated at: 2021-02-11 22:37:13 UTC  
> Merged at: 2021-02-11 22:37:09 UTC  
> Closed at: 2021-02-11 22:37:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/798  

Reported via ML https://lists.boost.org/geometry/2021/01/3829.php

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2021-01-27 11:16:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/798#pullrequestreview-577229853  

📁 include/boost/geometry/extensions/algorithms/detail/overlay/dissolver.hpp

```diff
 435 | 
 436 |-         int n = boost::size(output_collection);
 436 |+         std::size_t n = boost::size(output_collection);
```

> Username: barendgehrels  
> Created_at: 2021-01-27 11:15:52 UTC  
> Updated_at: 2021-01-27 11:16:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/798#discussion_r565226432  

Thanks


---
