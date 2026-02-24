# #22 Don't include non-existent header on WinCE [Closed]

> Username: muggenhor  
> Created at: 2015-12-16 17:42:01 UTC  
> Updated at: 2015-12-21 10:35:31 UTC  
> Closed at: 2015-12-20 12:18:59 UTC  
> Url: https://github.com/boostorg/log/pull/22  

Don't include non-existent header on WinCE

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-12-20 11:38:10 UTC  
> Url: https://github.com/boostorg/log/pull/22#issuecomment-166111356  

If there isn't `intrin.h` then where the `__cpuid` intrinsic is defined? If it's not supported on Windows CE then the whole SSE/AVX optimization should be disabled instead.

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-12-20 12:18:59 UTC  
> Url: https://github.com/boostorg/log/pull/22#issuecomment-166112879  

See if 88ec57d fixes the problem.

---

## Comment 3

> Username: muggenhor  
> Created_at: 2015-12-20 12:27:26 UTC  
> Url: https://github.com/boostorg/log/pull/22#issuecomment-166113202  

That seems like a better solution. I'm targeting an ARM system so obviously SSE and the x86 specific CPUID instruction isn't available (not sure about AVX as this is the first time I recall seeing the term).  
  
I'll test your fix tomorrow and let you know the result.

---

## Comment 4

> Username: muggenhor  
> Created_at: 2015-12-21 10:01:51 UTC  
> Url: https://github.com/boostorg/log/pull/22#issuecomment-166256931  

@Lastique thanks: the fix in 88ec57d works (as expected)!

---
