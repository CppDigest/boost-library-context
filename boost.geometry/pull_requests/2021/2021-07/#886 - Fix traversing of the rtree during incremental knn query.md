# #886 Fix traversing of the rtree during incremental knn query. [Merged]

> Username: awulkiew  
> Created at: 2021-07-07 21:00:44 UTC  
> Updated at: 2021-07-19 11:15:02 UTC  
> Merged at: 2021-07-19 11:15:02 UTC  
> Closed at: 2021-07-19 11:15:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/886  

Before this PR unneeded nodes were traversed by the query iterator and by extension many unneeded neighbours were taken into consideration. This fixes: https://github.com/boostorg/geometry/issues/867  
  
Additional improvement is that the neighbors that were already handled are not sorted again.  
  
This PR also changes type `unsigned` to `size_t` in several places.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-07-08 13:52:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/886#pullrequestreview-702091038  

Looks OK to me.

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-07-14 08:41:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/886#pullrequestreview-706008842  

Looks fine to me

---
