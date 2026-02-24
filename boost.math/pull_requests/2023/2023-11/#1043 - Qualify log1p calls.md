# #1043 Qualify log1p calls. [Merged]

> Username: jzmaddock  
> Created at: 2023-11-02 12:14:29 UTC  
> Updated at: 2024-01-24 21:33:40 UTC  
> Merged at: 2023-11-02 17:55:23 UTC  
> Closed at: 2023-11-02 17:55:23 UTC  
> Url: https://github.com/boostorg/math/pull/1043  

This fixes ambiguous overloads in the multiprecision case between boost::multiprecision and boost::math versions of log1p. See https://github.com/boostorg/multiprecision/pull/568#issuecomment-1790195318

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-11-02 17:55:16 UTC  
> Url: https://github.com/boostorg/math/pull/1043#issuecomment-1791264268  

Failure is unrelated (cohen_accelleration_test.cpp fails on cygwin - tolerance too tight?).

---
