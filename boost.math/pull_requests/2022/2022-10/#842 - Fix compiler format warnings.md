# #842 Fix compiler format warnings [Closed]

> Username: SiliconA-Z  
> Created at: 2022-10-14 19:53:16 UTC  
> Updated at: 2022-10-15 15:58:52 UTC  
> Closed at: 2022-10-15 15:58:52 UTC  
> Url: https://github.com/boostorg/math/pull/842  



---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2022-10-14 21:01:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/842#pullrequestreview-1143010879  

📁 include/boost/math/special_functions/legendre.hpp

```diff
  65 |- 
  66 |-    while(n < l)
  64 |+    for (unsigned n = 1; n < l; ++n)
```

> Username: mborland  
> Created_at: 2022-10-14 21:01:18 UTC  
> Updated_at: 2022-10-14 21:01:35 UTC  
> Url: https://github.com/boostorg/math/pull/842#discussion_r996151228  

What warning is this going to fix?

> Username: SiliconA-Z  
> Created_at: 2022-10-15 15:13:17 UTC  
> Url: https://github.com/boostorg/math/pull/842#discussion_r996318700  

This looks better.

> Username: NAThompson  
> Created_at: 2022-10-15 15:46:23 UTC  
> Url: https://github.com/boostorg/math/pull/842#discussion_r996322134  

I confess to some discomfort with this reasoning. It seems like a very easy way to introduce regressions, moreover it could lead to a veritable infinitude of pull requests where we all make minor changes to each other's code based on aesthetic criteria which are not globally shared.


---
