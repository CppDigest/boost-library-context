# #581 Fix disjoint Box/Poly and Box/Seg [Merged]

> Username: awulkiew  
> Created at: 2019-03-26 16:50:07 UTC  
> Updated at: 2019-03-27 14:09:00 UTC  
> Merged at: 2019-03-27 14:09:00 UTC  
> Closed at: 2019-03-27 14:09:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/581  

This fixes issues https://github.com/boostorg/geometry/issues/466 and https://github.com/boostorg/geometry/issues/579. The latter is fixed by commenting out the `alp1` parameter so it's similar to the proposed fix https://github.com/boostorg/geometry/pull/580 but here the code is not removed in case we decided to keep the parameter. I'm fine with removing it entirely so merging both PRs.

---
