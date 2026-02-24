# #1296 Some simplifications  [Merged]

> Username: vissarion  
> Created at: 2024-07-26 14:38:15 UTC  
> Updated at: 2024-09-30 08:58:41 UTC  
> Merged at: 2024-09-30 08:58:38 UTC  
> Closed at: 2024-09-30 08:58:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1296  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-07-27 09:42:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1296#pullrequestreview-2203150842  

📁 include/boost/geometry/algorithms/detail/is_simple/linear.hpp

```diff
  76 |-          && turn.operations[1].seg_id.segment_index == last_index);
  71 |+     return turn.operations[0].seg_id.segment_index == 0
  72 |+         && turn.operations[1].seg_id.segment_index == last_index;
```

> Username: barendgehrels  
> Created_at: 2024-07-27 09:42:19 UTC  
> Updated_at: 2024-07-27 09:42:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1296#discussion_r1693930482  

I think what was meant here was that the second condition had `1` vs `0` instead.  
But it's hard to guess. I didn't write it (I think). @awulkiew ?

> Username: vissarion  
> Created_at: 2024-08-09 13:35:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1296#discussion_r1711478814  

OK, I kept the second condition with reversed `0` and `1`.   
  
Out of curiosity, is it possible for the second condition to be true in the way turns are computed in boost geometry?

> Username: barendgehrels  
> Created_at: 2024-08-20 06:26:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1296#discussion_r1722754061  

Yes, I think so. But maybe better to check that before merging it.  
I'll try to check one of the coming days.

> Username: barendgehrels  
> Created_at: 2024-09-28 08:36:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1296#discussion_r1779444855  

@vissarion I forgot, and don't have the time to do it now in detail (it would require a quite specific testcase).  
The operations are symmetric (have the same role) and unordered, so I'm quite sure it's right now.  
Feel free to merge it.


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2024-07-27 09:43:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1296#pullrequestreview-2203150995  

Thanks! One question but good looks

---
