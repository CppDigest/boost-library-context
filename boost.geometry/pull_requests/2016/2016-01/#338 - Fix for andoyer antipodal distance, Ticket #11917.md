# #338 Fix for andoyer antipodal distance, Ticket #11917 [Closed]

> Username: kenba  
> Created at: 2016-01-17 11:19:48 UTC  
> Updated at: 2016-01-25 18:43:05 UTC  
> Closed at: 2016-01-25 18:43:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/338  

Calculate distance between antipodal points, i.e.  when C is zero.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2016-01-19 00:00:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/338#issuecomment-172688292  

Thanks for the PR!  
  
There is an implementation of Andoyer inverse formula capable to calculate both distance and azimuth:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/andoyer_inverse.hpp  
I also handled antipodal points there in #339.  
  
So an alternative to this PR could be to use thie fixed `andoyer_inverse` formula in `andoyer` strategy.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2016-01-25 18:42:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/338#issuecomment-174616628  

The implementation of `andoyer` distance strategy was replaced with the new one so this fix is not needed.

---
