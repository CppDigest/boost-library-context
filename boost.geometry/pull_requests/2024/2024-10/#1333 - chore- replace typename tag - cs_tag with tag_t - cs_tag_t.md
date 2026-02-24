# #1333 chore: replace typename tag / cs_tag with tag_t / cs_tag_t [Merged]

> Username: barendgehrels  
> Created at: 2024-10-30 14:45:24 UTC  
> Updated at: 2024-10-31 19:04:37 UTC  
> Merged at: 2024-10-31 19:04:30 UTC  
> Closed at: 2024-10-31 19:04:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1333  



---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2024-10-31 14:26:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1333#pullrequestreview-2408130374  

LGTM thanks!

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 449 |             <
 450 |-                 typename geometry::cs_tag<point_type>::type,
 450 |+                 geometry::cs_tag_t<point_type>,
```

> Username: vissarion  
> Created_at: 2024-10-31 14:25:13 UTC  
> Updated_at: 2024-10-31 14:26:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1333#discussion_r1824569599  

could you update the commit message that it also replaces cs_tag with cs_tag_t?

> Username: barendgehrels  
> Created_at: 2024-10-31 18:05:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1333#discussion_r1824947497  

✅ good point, done


---
