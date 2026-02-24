# #56 Removed NO_VOID_RETURNS workaround [Merged]

> Username: Kojoley  
> Created at: 2019-01-05 21:59:15 UTC  
> Updated at: 2019-01-06 11:01:36 UTC  
> Merged at: 2019-01-06 06:25:26 UTC  
> Closed at: 2019-01-06 06:25:26 UTC  
> Url: https://github.com/boostorg/variant/pull/56  

The workaround is obsolete, from Boost.Config it looks like it was used for very old EDG2.4, VC6, and other compilers in VC6 emulation mode.  
  
I have checked MSVC 9.0, GCC 4.1.2, Clang 3.0.0, and ICC 13.0.1 - all of them do allow void return. https://godbolt.org/z/9zS4TS

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-01-05 22:38:07 UTC  
> Updated_at: 2019-01-06 00:35:21 UTC  
> Url: https://github.com/boostorg/variant/pull/56#issuecomment-451697736  

[![Coverage Status](https://coveralls.io/builds/20923389/badge)](https://coveralls.io/builds/20923389)  
  
Coverage remained the same at 95.063% when pulling **c4370b2da2ae1fe6a6cdc0317fd8638596b0cd37 on Kojoley:remove-no-void-returns-workaround** into **6900641f4f1984e4c04d442791d08caa6f2710a4 on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-01-06 06:25:44 UTC  
> Url: https://github.com/boostorg/variant/pull/56#issuecomment-451718933  

Awesome! Many thanks!

---
