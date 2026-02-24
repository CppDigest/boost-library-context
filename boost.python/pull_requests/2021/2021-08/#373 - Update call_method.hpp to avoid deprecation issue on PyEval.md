# #373 Update call_method.hpp to avoid deprecation issue on PyEval [Merged]

> Username: da115115  
> Created at: 2021-08-15 15:06:05 UTC  
> Updated at: 2022-03-19 17:06:49 UTC  
> Merged at: 2022-03-19 17:06:49 UTC  
> Closed at: 2022-03-19 17:06:49 UTC  
> Url: https://github.com/boostorg/python/pull/373  

Was missing from https://github.com/boostorg/python/pull/320  
I've tested it on one of my projects with (that patch on) Boost.Python/Boost 1.76.0 and it works well. Without that patch, there is a deprecation error.

---

## Comment 1

> Username: hdu-sdlzx  
> Created_at: 2022-03-19 16:45:19 UTC  
> Updated_at: 2022-03-19 16:45:43 UTC  
> Url: https://github.com/boostorg/python/pull/373#issuecomment-1073041789  

ping @stefanseefeld

---

## Review 2 [Approved]

> Username: stefanseefeld  
> Created_at: 2022-03-19 17:06:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/python/pull/373#pullrequestreview-915019403  

Thanks !

---
