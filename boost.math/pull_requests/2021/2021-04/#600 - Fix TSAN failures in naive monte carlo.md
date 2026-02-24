# #600 Fix TSAN failures in naive monte carlo [Merged]

> Username: mborland  
> Created at: 2021-04-04 11:09:06 UTC  
> Updated at: 2021-04-05 17:39:42 UTC  
> Merged at: 2021-04-05 16:06:40 UTC  
> Closed at: 2021-04-05 16:06:41 UTC  
> Url: https://github.com/boostorg/math/pull/600  

Fix for issue #598.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-04-04 16:39:41 UTC  
> Url: https://github.com/boostorg/math/pull/600#issuecomment-813062458  

Thanks Matt, it's a shame we can have atomic shared_ptr's, but not exception_ptr's

---

## Comment 2

> Username: mborland  
> Created_at: 2021-04-05 16:01:05 UTC  
> Url: https://github.com/boostorg/math/pull/600#issuecomment-813471612  

This should be good to go; I re-arranged the test slightly to fix the one CI failure.

---
