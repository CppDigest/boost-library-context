# #867 lru_cache, remove redundant copy when updating queue position [Open]

> Username: Hailios  
> Created at: 2021-08-19 08:58:22 UTC  
> Updated at: 2021-10-05 13:33:22 UTC  
> Url: https://github.com/boostorg/compute/pull/867  



---

## Review 1 [Commented]

> Username: faker2048  
> Created_at: 2021-10-04 05:59:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/867#pullrequestreview-769890540  

📁 include/boost/compute/detail/lru_cache.hpp

```diff
 101 | 
 102 |             // return the value
 103 |             return value;
```

> Username: faker2048  
> Created_at: 2021-10-04 05:59:05 UTC  
> Updated_at: 2021-10-04 05:59:06 UTC  
> Url: https://github.com/boostorg/compute/pull/867#discussion_r721062581  

Where is the value from.

> Username: Hailios  
> Created_at: 2021-10-05 13:33:22 UTC  
> Updated_at: 2021-10-05 13:33:23 UTC  
> Url: https://github.com/boostorg/compute/pull/867#discussion_r722246405  

thanks, I forgot to update it. updated the PR now :)


---
