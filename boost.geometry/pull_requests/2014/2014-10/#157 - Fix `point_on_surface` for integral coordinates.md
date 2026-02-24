# #157 Fix `point_on_surface` for integral coordinates [Merged]

> Username: zerebubuth  
> Created at: 2014-10-13 03:43:17 UTC  
> Updated at: 2014-10-13 21:17:47 UTC  
> Merged at: 2014-10-13 10:36:00 UTC  
> Closed at: 2014-10-13 10:36:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/157  

Vector calculation should preserve precision in the calculation of extreme points to handle the case where `base_diff / diff` would be fractional and rounded to zero under integer arithmetic.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-10-13 21:17:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/157#issuecomment-58956211  

Thanks

---
