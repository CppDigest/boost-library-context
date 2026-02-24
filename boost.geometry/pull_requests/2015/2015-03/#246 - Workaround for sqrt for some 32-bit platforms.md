# #246 Workaround for sqrt for some 32-bit platforms [Merged]

> Username: mkaravel  
> Created at: 2015-03-07 07:32:39 UTC  
> Updated at: 2015-03-08 14:54:11 UTC  
> Merged at: 2015-03-08 14:14:30 UTC  
> Closed at: 2015-03-08 14:14:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/246  

Implement workaround for 32-bit platforms for which `sqrt(NaN)` returns a finite value instead of `NaN`. Workaround is activated by defining the macro `BOOST_GEOMETRY_SQRT_CHECK_FINITENESS`.  
  
I would like this PR to become part of 1.58.

---
