# #765 Fix for issue #764 [Merged]

> Username: mborland  
> Created at: 2022-02-22 16:41:50 UTC  
> Updated at: 2022-03-06 15:24:48 UTC  
> Merged at: 2022-02-23 09:06:13 UTC  
> Closed at: 2022-02-23 09:06:13 UTC  
> Url: https://github.com/boostorg/math/pull/765  



---

## Comment 1

> Username: mborland  
> Created_at: 2022-02-23 09:06:05 UTC  
> Url: https://github.com/boostorg/math/pull/765#issuecomment-1048571731  

Only failure if CI package installation so merging. Gets us back to green.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-02-23 12:45:33 UTC  
> Url: https://github.com/boostorg/math/pull/765#issuecomment-1048745921  

Can we not fix the source?  It looks like something is including C++ headers in C mode?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-02-23 13:24:18 UTC  
> Url: https://github.com/boostorg/math/pull/765#issuecomment-1048778135  

Unable to reproduce with msvc-14.3.0, once the huge download I have in progress completes I'll update to 14.3.1 and try again.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-03-06 15:24:47 UTC  
> Url: https://github.com/boostorg/math/pull/765#issuecomment-1059982774  

Reproduced, dealt with upstream in Boost.Config.

---
