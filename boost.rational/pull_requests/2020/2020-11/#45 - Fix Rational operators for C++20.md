# #45 Fix Rational operators for C++20 [Merged]

> Username: glenfe  
> Created at: 2020-11-15 15:41:59 UTC  
> Updated at: 2020-11-16 14:31:25 UTC  
> Merged at: 2020-11-16 14:31:25 UTC  
> Closed at: 2020-11-16 14:31:25 UTC  
> Url: https://github.com/boostorg/rational/pull/45  

Based on C++20 rules, one equality operator ends up calling itself recursively. Tests now pass with GCC 10 (before two failed).

---

## Comment 1

> Username: mclow  
> Created_at: 2020-11-16 14:23:37 UTC  
> Url: https://github.com/boostorg/rational/pull/45#issuecomment-728093784  

LGTM.

---
