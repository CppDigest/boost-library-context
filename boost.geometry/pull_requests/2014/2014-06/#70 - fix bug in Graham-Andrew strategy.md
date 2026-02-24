# #70 [strategies][agnostic] fix bug in Graham-Andrew strategy: [Closed]

> Username: mkaravel  
> Created at: 2014-06-20 09:20:34 UTC  
> Updated at: 2014-07-01 23:24:38 UTC  
> Closed at: 2014-06-20 10:31:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/70  

last is used again after two calls to pop_back() which can invalidate the reference;  
patch: make last a true point rather than a reference  
bug reported/patch suggested by David Zhao

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-06-20 10:31:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/70#issuecomment-46665238  

This might not be a bug after all.

---
