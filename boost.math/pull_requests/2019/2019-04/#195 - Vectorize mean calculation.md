# #195 Vectorize mean calculation. [Merged]

> Username: NAThompson  
> Created at: 2019-04-13 21:22:48 UTC  
> Updated at: 2019-04-14 14:32:19 UTC  
> Merged at: 2019-04-14 14:32:12 UTC  
> Closed at: 2019-04-14 14:32:12 UTC  
> Url: https://github.com/boostorg/math/pull/195  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2019-04-14 10:49:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/195#pullrequestreview-226389281  

📁 include/boost/math/tools/univariate_statistics.hpp

```diff
  32 |     }
  33 |-     else
  33 |+     else if constexpr (std::is_same_v<typename std::iterator_traits<ForwardIterator>::iterator_category, std::random_access_iterator_tag>)
```

> Username: jzmaddock  
> Created_at: 2019-04-14 10:49:54 UTC  
> Url: https://github.com/boostorg/math/pull/195#discussion_r275150906  

Is this otherwise dependent upon C++17?  If yes, please go ahead and merge, otherwise can we add some configuration logic to fall back to the old code for non-C++ 17 compilers?

> Username: NAThompson  
> Created_at: 2019-04-14 14:32:06 UTC  
> Url: https://github.com/boostorg/math/pull/195#discussion_r275159704  

Yeah, the whole file uses C++17.


---
