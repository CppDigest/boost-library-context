# #685 Fix cstdfloat_math_incl_test on ppc64le with ieee128 [Merged]

> Username: mscastanho  
> Created at: 2021-09-01 20:45:45 UTC  
> Updated at: 2021-09-21 18:01:23 UTC  
> Merged at: 2021-09-21 18:01:23 UTC  
> Closed at: 2021-09-21 18:01:23 UTC  
> Url: https://github.com/boostorg/math/pull/685  

When switching the default long double format on ppc64le to ieee128, this test  
does not compile as nanq is not available. In such cases nanl should be used  
instead.  
  
Also, calling nanl with argument 0 raises warnings as it expects a char*. Change  
the argument to an empty string, which keeps the purpose of the test and avoids  
new warnings.  
  
Related to #682

---

## Comment 1

> Username: mscastanho  
> Created_at: 2021-09-21 17:59:18 UTC  
> Url: https://github.com/boostorg/math/pull/685#issuecomment-924226786  

Ping?

---
