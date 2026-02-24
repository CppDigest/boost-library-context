# #639 R-tree improvements. [Merged]

> Username: awulkiew  
> Created at: 2019-11-12 17:13:59 UTC  
> Updated at: 2020-01-24 21:44:29 UTC  
> Merged at: 2020-01-24 21:44:29 UTC  
> Closed at: 2020-01-24 21:44:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/639  

- improve readability/maintainability of the code by replacing lists of types in visitors with one type of `MembersHolder` and getting all needed types from it.  
- improve exception safety by removing exceeding element if there are too many elements in a node after exception is thrown in `split`, so even if a user chooses to try to insert something into the rtree, even though the state of the rtree is not valid the new element won't be put out of bounds which now would result in assert or segfault. (I consider it a bug).

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-01-20 07:58:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/639#pullrequestreview-345126907  

Thanks, I am ok with merging!

---
