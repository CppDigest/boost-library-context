# #275 A few updates for promote_integral [Merged]

> Username: mkaravel  
> Created at: 2015-03-27 23:40:08 UTC  
> Updated at: 2015-03-28 01:25:49 UTC  
> Merged at: 2015-03-28 01:22:55 UTC  
> Closed at: 2015-03-28 01:22:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/275  

In this PR:  
- The inline documentation for `promote_integral<>` is updated.  
- All size measurements are done in bits (rather than mixing bits and bytes).  
- Code is polished a bit.  
- Choice for the promoted type is based on bit size rather than on the outcome of the `sizeof()` operator.

---
