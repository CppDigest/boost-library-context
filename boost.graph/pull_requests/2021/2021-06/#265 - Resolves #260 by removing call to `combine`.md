# #265 Resolves #260 by removing call to `combine` [Merged]

> Username: qbit86  
> Created at: 2021-06-03 21:03:06 UTC  
> Updated at: 2022-03-03 23:55:13 UTC  
> Merged at: 2022-03-03 23:55:13 UTC  
> Closed at: 2022-03-03 23:55:13 UTC  
> Url: https://github.com/boostorg/graph/pull/265  



---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2021-12-09 21:35:01 UTC  
> Url: https://github.com/boostorg/graph/pull/265#issuecomment-990289291  

Have you had a chance to step through this with the debugger and convince yourself that it truly has no effect?

---

## Comment 2

> Username: qbit86  
> Created_at: 2021-12-09 22:30:37 UTC  
> Url: https://github.com/boostorg/graph/pull/265#issuecomment-990354577  

> Have you had a chance to step through this with the debugger and convince yourself that it truly has no effect?  
  
This function is provided by the user side as a part of monoid contract. It can have any observable effects as the user desires. Usually it is just the pure function adding two numbers. But of course it can format the user's disk or whatever she wants.  
  
So actual effects are not the point of this issue. The point is that this call makes no sense in the context of A-star algorithm.

---
