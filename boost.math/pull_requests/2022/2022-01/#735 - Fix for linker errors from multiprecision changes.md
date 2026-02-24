# #735 Fix for linker errors from multiprecision changes. [Merged]

> Username: jzmaddock  
> Created at: 2022-01-07 11:38:13 UTC  
> Updated at: 2022-01-08 17:02:27 UTC  
> Merged at: 2022-01-08 14:39:06 UTC  
> Closed at: 2022-01-08 14:39:06 UTC  
> Url: https://github.com/boostorg/math/pull/735  

Fixes https://github.com/boostorg/math/issues/733.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-01-07 13:44:25 UTC  
> Url: https://github.com/boostorg/math/pull/735#issuecomment-1007418737  

Nice!  
  
Just to be sure... So MSVC build(s) simply remain unchanged, right? And do not erroneously try to use 128-bit artifacts either?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-01-07 17:53:38 UTC  
> Url: https://github.com/boostorg/math/pull/735#issuecomment-1007609119  

>Just to be sure... So MSVC build(s) simply remain unchanged, right? And do not erroneously try to use 128-bit artifacts either?  
  
Correct.  You only hit this if __float128 is an available type, and your precision is < 113 bits: in which case you should probably be using __float128 in the first place.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-01-08 14:38:58 UTC  
> Url: https://github.com/boostorg/math/pull/735#issuecomment-1008008650  

Failures are CI SNAFU's, merging...

---
