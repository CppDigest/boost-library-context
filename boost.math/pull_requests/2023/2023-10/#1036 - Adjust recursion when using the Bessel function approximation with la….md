# #1036 Adjust recursion when using the Bessel function approximation with la… [Merged]

> Username: jzmaddock  
> Created at: 2023-10-11 10:46:03 UTC  
> Updated at: 2023-10-13 15:12:47 UTC  
> Merged at: 2023-10-13 09:34:20 UTC  
> Closed at: 2023-10-13 09:34:20 UTC  
> Url: https://github.com/boostorg/math/pull/1036  

…rge z.  
  
Avoids hitting tgamma on a negative integer, also improves accuracy when b-a-0.5 is close to a negative integer. Fixes https://github.com/boostorg/math/issues/1034.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-10-13 09:34:14 UTC  
> Url: https://github.com/boostorg/math/pull/1036#issuecomment-1761214729  

CI failures are network/clone issues, merging.

---
