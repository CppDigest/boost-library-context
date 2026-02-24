# #908 Fix robust convex hull tests [Merged]

> Username: vissarion  
> Created at: 2021-09-23 11:45:14 UTC  
> Updated at: 2022-11-16 14:14:56 UTC  
> Merged at: 2021-09-23 11:55:29 UTC  
> Closed at: 2021-09-23 11:55:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/908  

Fix tests after non robust convex hull (using the fast non robust orientation predicate) returns wrong results, which is OK and expected since the computation is non robust.

---
