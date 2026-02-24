# #28 Don't crash on pthread_cancel [Closed]

> Username: justinasvd  
> Created at: 2019-03-13 20:26:56 UTC  
> Updated at: 2019-03-14 14:04:23 UTC  
> Closed at: 2019-03-14 14:04:23 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/28  

This patch prevents `abi::__forced_unwind` from being eaten and avoids a `FATAL: exception not rethrown` error. This, however, is only part of the solution for the problem, since additional support is also needed from the context here.

---

## Review 1 [Commented]

> Username: justinasvd  
> Created_at: 2019-03-14 07:49:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/coroutine2/pull/28#pullrequestreview-214356791  

📁 include/boost/coroutine2/detail/config.hpp

```diff
  42 |+ # include <cxxabi.h>
  43 |+ #endif
  44 |+ 
```

> Username: justinasvd  
> Created_at: 2019-03-14 07:49:16 UTC  
> Updated_at: 2019-03-14 07:49:17 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/28#discussion_r265449864  

Personally, I think that this config knob should be in Boost.Context, and the `BOOST_COROUTINES2_HAS_CXXABI_H` should be called `BOOST_CONTEXT_HAS_CXXABI_H`, but I didn't want to spread this patch over two repos.


---

## Comment 2

> Username: olk  
> Created_at: 2019-03-14 14:04:23 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/28#issuecomment-472870044  

ty - I've split the pull request

---
