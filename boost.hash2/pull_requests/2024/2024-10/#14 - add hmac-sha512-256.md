# #14 add hmac-sha512/256 [Merged]

> Username: cmazakas  
> Created at: 2024-10-17 15:58:49 UTC  
> Updated at: 2024-10-17 17:32:20 UTC  
> Merged at: 2024-10-17 16:43:07 UTC  
> Closed at: 2024-10-17 16:43:08 UTC  
> Url: https://github.com/boostorg/hash2/pull/14  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-10-17 16:42:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hash2/pull/14#pullrequestreview-2375762070  

📁 include/boost/hash2/sha2.hpp

```diff
 574 | 
 575 |     using result_type = std::array<unsigned char, 32>;
 576 |+     using size_type = std::size_t;
```

> Username: pdimov  
> Created_at: 2024-10-17 16:42:37 UTC  
> Updated_at: 2024-10-17 16:42:38 UTC  
> Url: https://github.com/boostorg/hash2/pull/14#discussion_r1805099854  

size_type should be of fixed size, in this case uint64_t.


---
