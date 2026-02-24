# #140 Fix use-after-move segfault [Merged]

> Username: cmazakas  
> Created at: 2022-08-17 21:14:20 UTC  
> Updated at: 2022-08-18 22:03:50 UTC  
> Merged at: 2022-08-18 21:54:08 UTC  
> Closed at: 2022-08-18 21:54:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/140  

Addresses https://github.com/boostorg/unordered/issues/139  
  
Add `post_move_tests` which test the moved-from state of all the unordered containers, including move construction, move assignment, move with equal/unequal allocators and POCMA.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2022-08-17 21:34:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/140#pullrequestreview-1076455148  

📁 include/boost/unordered/unordered_map.hpp

```diff
2071 |     {
2072 |+       if (table_.size_ == 0) {
2073 |+         return 0.0;
```

> Username: pdimov  
> Created_at: 2022-08-17 21:34:33 UTC  
> Updated_at: 2022-08-17 21:34:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/140#discussion_r948446079  

Should be `0.0f`.


---
