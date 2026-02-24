# #302 Bug-fix for one-sided cropping [Merged]

> Username: HDembinski  
> Created at: 2020-12-07 12:36:45 UTC  
> Updated at: 2020-12-07 14:12:28 UTC  
> Merged at: 2020-12-07 14:12:21 UTC  
> Closed at: 2020-12-07 14:12:21 UTC  
> Url: https://github.com/boostorg/histogram/pull/302  

Fixes a bug when `reduce` is used with one-sided `crop`, where "one-sided" means that the cropping is supposed to remove only the upper part of an axis including the overflow bin, but keep the lower end including the underflow bin or vice versa.  
  
The current (wrong) behavior of `crop` is to always remove the *flow contents on both ends.

---
