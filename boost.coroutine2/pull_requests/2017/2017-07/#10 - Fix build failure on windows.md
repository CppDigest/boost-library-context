# #10 Fix build failure on windows [Merged]

> Username: mog422  
> Created at: 2017-07-06 08:35:11 UTC  
> Updated at: 2017-07-06 08:50:11 UTC  
> Merged at: 2017-07-06 08:50:07 UTC  
> Closed at: 2017-07-06 08:50:07 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/10  

coroutine2 is a header-only library. But, It includes auto_link.hpp.  
That worked surprisingly smoothly until before boost 1.64.  
But the commit 91c327bb019f3eca3d4fd2f565557f6926d4d93b causes a build failure.

---

## Comment 1

> Username: olk  
> Created_at: 2017-07-06 08:50:11 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/10#issuecomment-313335872  

thx

---
