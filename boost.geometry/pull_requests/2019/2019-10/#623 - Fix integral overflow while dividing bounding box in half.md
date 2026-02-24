# #623 [partition] Fix integral overflow while dividing bounding box in half. [Merged]

> Username: awulkiew  
> Created at: 2019-10-08 00:51:12 UTC  
> Updated at: 2019-10-19 15:57:07 UTC  
> Merged at: 2019-10-19 15:57:06 UTC  
> Closed at: 2019-10-19 15:57:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/623  



---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2019-10-08 17:26:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/623#pullrequestreview-298925356  

📁 include/boost/geometry/algorithms/detail/partition.hpp

```diff
  49 |+     {
  50 |+         // avoid overflow
  51 |+         return mi / 2 + ma / 2 + (mi % 2 + ma % 2) / 2;
```

> Username: barendgehrels  
> Created_at: 2019-10-08 17:26:40 UTC  
> Updated_at: 2019-10-08 17:26:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/623#discussion_r332636979  

OK for me. Thanks for the fix.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2019-10-08 19:26:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/623#pullrequestreview-298995224  

Thanks, I am ok with merging.

---
