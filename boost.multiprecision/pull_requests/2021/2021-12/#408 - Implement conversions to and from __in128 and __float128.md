# #408 Implement conversions to and from __in128 and __float128. [Merged]

> Username: jzmaddock  
> Created at: 2021-12-28 19:12:03 UTC  
> Updated at: 2022-01-06 18:24:14 UTC  
> Merged at: 2022-01-06 18:24:14 UTC  
> Closed at: 2022-01-06 18:24:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/408  

Add tests to test_arithmetic.hpp.  
Strengthened up conversion/assignment operators with enable_if as required, so that unsupported conversions lead to errors at the top level (when looking for the operator in class number), rather than deep inside our template code.  
Fixes: https://github.com/boostorg/multiprecision/issues/237.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-01-06 18:24:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/408#issuecomment-1006814994  

Failures are CI SNAFU's, merging...

---
