# #44 xxh3 cleanup [Merged]

> Username: cmazakas  
> Created at: 2025-05-07 19:51:24 UTC  
> Updated at: 2025-05-09 14:39:17 UTC  
> Merged at: 2025-05-08 23:27:51 UTC  
> Closed at: 2025-05-08 23:27:51 UTC  
> Url: https://github.com/boostorg/hash2/pull/44  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2025-05-08 14:35:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hash2/pull/44#pullrequestreview-2825363185  

📁 include/boost/hash2/xxh3.hpp

```diff
 546 |     {
 542 |-         with_secret_ = true;
 547 |+         with_secret_ = with_secret;
```

> Username: pdimov  
> Created_at: 2025-05-08 14:35:28 UTC  
> Url: https://github.com/boostorg/hash2/pull/44#discussion_r2079849350  

I suppose this can now go in the member initializer list, for aesthetic reasons if nothing else.


---
