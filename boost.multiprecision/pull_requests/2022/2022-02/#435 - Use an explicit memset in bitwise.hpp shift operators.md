# #435 Use an explicit memset in bitwise.hpp shift operators. [Merged]

> Username: jzmaddock  
> Created at: 2022-02-07 18:57:38 UTC  
> Updated at: 2022-02-09 15:35:23 UTC  
> Merged at: 2022-02-09 15:35:23 UTC  
> Closed at: 2022-02-09 15:35:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/435  

Rather than relying on compiler generated transformation to memset, as the latter results in spurious GCC warnings.  
Fixes https://github.com/boostorg/multiprecision/issues/434.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-02-09 15:35:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/435#issuecomment-1033891000  

Failing test is a network timeout, merging...

---
