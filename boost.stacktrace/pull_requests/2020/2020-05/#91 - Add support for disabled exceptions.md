# #91 Add support for disabled exceptions [Merged]

> Username: jeremiahar  
> Created at: 2020-05-26 20:33:48 UTC  
> Updated at: 2020-05-28 13:48:54 UTC  
> Merged at: 2020-05-28 13:48:43 UTC  
> Closed at: 2020-05-28 13:48:43 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/91  

This fixes a build error when compiled with -fno-exceptions:  
  
> /usr/include/boost/stacktrace/stacktrace.hpp:98:11: error: exception handling disabled, use ‘-fexceptions’ to enable  
   98 |         } catch (...) {

---

## Comment 1

> Username: coveralls  
> Created_at: 2020-05-26 20:58:36 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/91#issuecomment-634275670  

[![Coverage Status](https://coveralls.io/builds/31045459/badge)](https://coveralls.io/builds/31045459)  
  
Coverage remained the same at 92.291% when pulling **a8a4cefb521bcb47aeac2b2a2f4cc055a685cdf1 on jeremiahar:develop** into **f931528c87f19757e060f5fee1a4f8ccdbacf7a0 on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2020-05-28 13:48:54 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/91#issuecomment-635361703  

Many thanks for the PR!

---
