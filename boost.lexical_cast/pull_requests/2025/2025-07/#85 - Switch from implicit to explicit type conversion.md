# #85 Switch from implicit to explicit type conversion [Merged]

> Username: bmagistro  
> Created at: 2025-07-29 13:46:21 UTC  
> Updated at: 2025-08-22 07:34:12 UTC  
> Merged at: 2025-08-22 07:33:58 UTC  
> Closed at: 2025-08-22 07:33:58 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/85  

When compiling with clang 19.1.7 on Alma with -Werror and a number of other flags (we run with most warnings enabled), the lines were flagged with `error: implicit conversion increases floating-point precision: 'float' to 'const double' [-Werror, -Wdouble-promotion]`. Based on the functions, it is believe that this should an explicit conversion which also clears the previously mentioned error.  To reduce code changes the variable was updated, but this could theoretically be done inline and remove the variable.  
  
Env:  
Alma 9.6  
Boost 1.88.0 via cmake  
clang 19.1.7

---

## Comment 1

> Username: apolukhin  
> Created_at: 2025-08-22 07:34:12 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/85#issuecomment-3213382528  

Many thanks for the PR!

---
