# #63 Do not call abort in forced_return [Merged]

> Username: Kojoley  
> Created at: 2019-03-26 01:50:59 UTC  
> Updated at: 2019-04-12 21:18:02 UTC  
> Merged at: 2019-04-12 07:54:17 UTC  
> Closed at: 2019-04-12 07:54:17 UTC  
> Url: https://github.com/boostorg/variant/pull/63  

Reduces code bloating on Clang and MSVC, saves a branch on GCC.  
The noreturn warnings have also been tested on https://godbolt.org/z/T5gmlV  
  
ref https://github.com/boostorg/variant/issues/60#issuecomment-476382413.

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-03-26 02:16:18 UTC  
> Updated_at: 2019-03-26 15:04:51 UTC  
> Url: https://github.com/boostorg/variant/pull/63#issuecomment-476447323  

[![Coverage Status](https://coveralls.io/builds/22411307/badge)](https://coveralls.io/builds/22411307)  
  
Coverage remained the same at 95.063% when pulling **0388c767ebf921dbe5c0aa137577c146c709b971 on Kojoley:do-not-call-abort** into **7ecf721f2b20df142d73d0dc1fe0677abb05c9d1 on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-03-26 05:30:26 UTC  
> Url: https://github.com/boostorg/variant/pull/63#issuecomment-476481272  

MSVC is unhappy. Looks as an issue in Boost.Config.

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-03-26 12:17:44 UTC  
> Url: https://github.com/boostorg/variant/pull/63#issuecomment-476600041  

Easiest would be something like  
```  
#if defined(__GNUC__)  
__builtin_unreachable();  
#elif defined(_MSC_VER)  
__assume(false);  
#else  
std::abort();  
#endif  
```

---
