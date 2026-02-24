# #931 [equals] Define collect_vectors type in use_collect_vectors [Merged]

> Username: awulkiew  
> Created at: 2021-10-30 23:15:16 UTC  
> Updated at: 2021-11-02 17:11:15 UTC  
> Merged at: 2021-11-02 17:11:01 UTC  
> Closed at: 2021-11-02 17:11:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/931  

After PR https://github.com/boostorg/geometry/pull/926  
  
I propose to define collect_vectors type in `use_collect_vectors`, so at the same place where this version is is enabled for specific side strategy and `cs_tag`.  
  
Furthermore there is no need to instantiate both versions of collect_vectors with `std::conditional` in `equals_by_collection_or_relate` so this PR changes that.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2021-10-31 16:28:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/931#pullrequestreview-793737288  

📁 include/boost/geometry/algorithms/detail/equals/implementation.hpp

```diff
 283 |+     {
 284 |+         return equals_by_relate<Geometry1, Geometry2>::apply(geometry1, geometry2, strategy);
 285 |     }
```

> Username: barendgehrels  
> Created_at: 2021-10-31 16:28:46 UTC  
> Updated_at: 2021-10-31 16:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/931#discussion_r739846113  

Thanks for this! I was already thinking how the two structures could be combined, but didn’t get this yet. Looks good!


---
