# #1013 Fixes warning with -Wduplicated-branches in overlay for union (issue #443). [Merged]

> Username: tinko92  
> Created at: 2022-06-06 21:33:42 UTC  
> Updated at: 2022-06-23 10:57:12 UTC  
> Merged at: 2022-06-23 10:57:12 UTC  
> Closed at: 2022-06-23 10:57:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1013  

Fixes #443 and (IMHO) slightly improves clarity.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-06-07 12:48:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1013#pullrequestreview-998084012  

LGTM but let's wait for Barend to approve this.

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2022-06-08 09:28:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1013#pullrequestreview-999412576  

📁 include/boost/geometry/algorithms/detail/overlay/overlay.hpp

```diff
 402 | #endif
 400 |-                                       : add_rings_ignore_unordered);
 403 |+                                       );
```

> Username: barendgehrels  
> Created_at: 2022-06-08 09:28:19 UTC  
> Updated_at: 2022-06-08 09:28:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1013#discussion_r892133638  

Thanks. Does anyone still remember why this only applies for union, and not for intersection or difference?

> Username: awulkiew  
> Created_at: 2022-06-08 12:05:11 UTC  
> Updated_at: 2022-06-08 12:05:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1013#discussion_r892274821  

I don't remember. It was added by me 5 years ago:  
https://github.com/boostorg/geometry/commit/0b8a704ae95da0698e0c5f8b56c358e8e370a8ec  
and then altered later:  
https://github.com/boostorg/geometry/commit/fde547514442eb4d67aac78d7bf792ca373f6890  
  
It seems it has something to do with spherical and geographic CS and the fact that if polygons are added together they may occupy more than half of the globe which would make holes from them because the area of such polygon would be negative. And other set operations cannot increase the size of the polygons in the result WRT the inputs.

> Username: barendgehrels  
> Created_at: 2022-06-15 06:28:54 UTC  
> Updated_at: 2022-06-15 06:28:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1013#discussion_r897582224  

All right, thanks!


---
