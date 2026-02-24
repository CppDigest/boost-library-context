# #21 Fix addr2line command line construction [Merged]

> Username: Lastique  
> Created at: 2017-06-25 22:17:43 UTC  
> Updated at: 2017-06-27 04:50:36 UTC  
> Merged at: 2017-06-27 04:49:02 UTC  
> Closed at: 2017-06-27 04:49:02 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/21  



---

## Comment 1

> Username: Lastique  
> Created_at: 2017-06-25 22:18:08 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/21#issuecomment-310932321  

Also, consider removing the sys/* includes.

---

## Comment 2

> Username: coveralls  
> Created_at: 2017-06-25 22:39:58 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/21#issuecomment-310933347  

[![Coverage Status](https://:/builds/12125275/badge)](https://:/builds/12125275)  
  
Coverage remained the same at 85.083% when pulling **9b7059f518cc81082252db3b3531ea7db9ad30c7 on Lastique:patch-1** into **4eaaaeec3a1c8ac4e90081ea0f53ef2957f1d405 on boostorg:develop**.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2017-06-27 04:50:36 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/21#issuecomment-311252318  

Thanks for the pull!  
  
Includes must remain in the source, because their existence must be also checked.

---
