# #162 - fix as much time-related issues as possible and improve the QOI [Closed]

> Username: viboes  
> Created at: 2017-09-23 06:02:09 UTC  
> Updated at: 2018-02-20 17:12:44 UTC  
> Closed at: 2018-02-20 17:12:43 UTC  
> Url: https://github.com/boostorg/thread/issues/162  

Some time-related functions doesn't work when the system-time backs and forwards the system clock (the return result is not correct in some cases).  
  
In addition the timeout is not as close as it could be.  
  
We have a branch (https://github.com/boostorg/thread/pull/142) for fixing these issues where   
  
* we have created some internal monotonic clock that help to provide some functionalities that were not possible without, and  
*in addition, to improve the QOI,  we pool when the platform doesn't provide a monotonic interface.

---

## Comment 1

> Username: viboes  
> Created at: 2018-02-20 17:12:43 UTC  
> Url: https://github.com/boostorg/thread/issues/162#issuecomment-367048756  

https://github.com/boostorg/thread/commit/9be099606262c1e213a8c30f95c2819deef44633
