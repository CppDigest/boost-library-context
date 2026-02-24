# #30 Fix narrowing conversions for ppc [Open]

> Username: jwakely  
> Created at: 2022-01-31 11:37:37 UTC  
> Updated at: 2025-12-09 12:10:07 UTC  
> Url: https://github.com/boostorg/interval/pull/30  

These constants are too large for `long long` so are unsigned,  
and then cannot be narrowed to the signed type.  
  
Fixes #29

---

## Comment 1

> Username: jwakely  
> Created_at: 2022-01-31 11:41:21 UTC  
> Url: https://github.com/boostorg/interval/pull/30#issuecomment-1025651398  

Before: https://godbolt.org/z/en14c9Ps9  
After: https://godbolt.org/z/nxPEbfcbf

---

## Comment 2

> Username: LocutusOfBorg  
> Created_at: 2023-07-20 08:24:11 UTC  
> Url: https://github.com/boostorg/interval/pull/30#issuecomment-1643494700  

Please accept this patch. It fixes ppc64el build error...  
See e.g. https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1040524

---

## Comment 3

> Username: jwakely  
> Created_at: 2025-12-09 12:10:07 UTC  
> Url: https://github.com/boostorg/interval/pull/30#issuecomment-3631932437  

Ping

---
