# #1029 Fix compilation of touches for geometry collections with gcc-10 [Merged]

> Username: awulkiew  
> Created at: 2022-07-01 23:26:03 UTC  
> Updated at: 2022-07-06 11:56:10 UTC  
> Merged at: 2022-07-06 11:56:10 UTC  
> Closed at: 2022-07-06 11:56:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1029  

Avoid -1 to size_t conversion while instantiating static mask. Relate is now called with proper mask based on run-time topological dimension of GCs just like it's done with other relational operations which masks depend on dimensions of arguments.  
  
Also fix warning in relate.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-07-06 10:39:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1029#pullrequestreview-1029879245  

thanks

---
