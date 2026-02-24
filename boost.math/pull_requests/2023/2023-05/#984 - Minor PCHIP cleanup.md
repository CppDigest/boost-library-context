# #984 Minor PCHIP cleanup [Merged]

> Username: NAThompson  
> Created at: 2023-05-06 20:44:27 UTC  
> Updated at: 2023-05-08 10:34:43 UTC  
> Merged at: 2023-05-08 10:34:38 UTC  
> Closed at: 2023-05-08 10:34:38 UTC  
> Url: https://github.com/boostorg/math/pull/984  

The comment that the finite difference approximation to the derivative was the only way to produce monotonicity was wrong.  
  
In addition, the error message gave no hint as to its origin. Fix this.

---
