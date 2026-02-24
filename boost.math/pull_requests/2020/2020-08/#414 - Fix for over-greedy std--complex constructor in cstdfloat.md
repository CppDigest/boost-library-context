# #414 Fix for over-greedy std::complex constructor in cstdfloat. [Merged]

> Username: jzmaddock  
> Created at: 2020-08-02 18:35:32 UTC  
> Updated at: 2020-08-03 18:20:56 UTC  
> Merged at: 2020-08-03 17:51:28 UTC  
> Closed at: 2020-08-03 17:51:29 UTC  
> Url: https://github.com/boostorg/math/pull/414  

cstdfloat: Make complex<__float128> converting constructor explicit  
and non-lossy construction from float/double/long double implicit.  
Fixes https://github.com/boostorg/math/issues/350.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2020-08-03 18:20:55 UTC  
> Url: https://github.com/boostorg/math/pull/414#issuecomment-668171330  

This is a very nice, clean and thoughtful fix. Thank you.

---
