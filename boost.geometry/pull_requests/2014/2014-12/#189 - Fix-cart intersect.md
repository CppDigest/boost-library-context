# #189 Fix/cart intersect [Merged]

> Username: awulkiew  
> Created at: 2014-12-27 22:15:08 UTC  
> Updated at: 2014-12-29 21:08:41 UTC  
> Merged at: 2014-12-29 21:08:38 UTC  
> Closed at: 2014-12-29 21:08:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/189  

This fixes ticket https://svn.boost.org/trac/boost/ticket/10904.  
  
In cart_intersect an invalid Dimension may be choosen for very small segments, i.e. the sum of differences between coordinates may be greater even if one of them is 0 which deeper causes to create a ratio with denominator equal to 0.

---
