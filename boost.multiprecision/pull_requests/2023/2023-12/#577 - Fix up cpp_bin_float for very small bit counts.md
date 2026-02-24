# #577 Fix up cpp_bin_float for very small bit counts. [Merged]

> Username: jzmaddock  
> Created at: 2023-12-15 12:18:56 UTC  
> Updated at: 2023-12-16 09:21:29 UTC  
> Merged at: 2023-12-16 09:21:29 UTC  
> Closed at: 2023-12-16 09:21:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/577  

Adds support for emulating float16_t and bfloat16_t. Also adds test cases, and updates test_arithmetic.hpp to cope with testing small bit count types. Fixes https://github.com/boostorg/multiprecision/issues/576.

---
