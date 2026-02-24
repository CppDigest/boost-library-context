# #411 Fix geographic area strategy for sphere. [Merged]

> Username: awulkiew  
> Created at: 2017-07-20 16:34:36 UTC  
> Updated at: 2017-08-30 12:22:03 UTC  
> Merged at: 2017-07-28 00:11:15 UTC  
> Closed at: 2017-07-28 00:11:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/411  

In this case `e2` is `0` which resulted in `c2` being `NaN` and at the end the final result being `NaN`. This PR sets `c2` to `a2` if `e2` is `0`.  
  
Plus unnecessary `sqrt()` call is removed.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-07-27 01:55:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/411#issuecomment-318235725  

@vissarion Could you have a look if everything is ok?

---

## Comment 2

> Username: vissarion  
> Created_at: 2017-07-27 09:54:39 UTC  
> Updated_at: 2017-07-27 09:54:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/411#issuecomment-318316003  

Fine regarding the bug-fix. Thanks.  
Regarding performance the strategy computes a lot of useless stuff in the a==b case. Spherical strategy should be used somehow or avoid computing quantities not used in a==b case. Anyway, this could be done in separate PR.

---
