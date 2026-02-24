# #20 Quaternion slerp() using incorrect precision [Merged]

> Username: DionHo  
> Created at: 2019-10-05 14:36:27 UTC  
> Updated at: 2019-10-05 15:13:11 UTC  
> Merged at: 2019-10-05 15:13:06 UTC  
> Closed at: 2019-10-05 15:13:06 UTC  
> Url: https://github.com/boostorg/qvm/pull/20  

In my use cases, the slerp operation returned a quaternion with nan-values because the angle between input quaternions was too low. I used double precision for the quaternions, but the acosf() function reduced the calculation accuracy, resulting in "theta==0.0".  
  
Using acos<TR> instead of acosf is consistent with the other trigonometrical functions used in this method and solves the problem of precision loss in my use case.  
  
(With even smaller angle differences, the calculation could still result in nan-quaternions when using double precision, but this is still an improvement, imho.)

---

## Comment 1

> Username: zajo  
> Created_at: 2019-10-05 15:13:11 UTC  
> Url: https://github.com/boostorg/qvm/pull/20#issuecomment-538658556  

This was clearly a bug, thank you!

---
