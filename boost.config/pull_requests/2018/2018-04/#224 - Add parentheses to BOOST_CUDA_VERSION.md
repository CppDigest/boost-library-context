# #224 Add parentheses to BOOST_CUDA_VERSION [Merged]

> Username: pdimov  
> Created at: 2018-04-28 17:32:45 UTC  
> Updated at: 2018-09-13 13:37:06 UTC  
> Merged at: 2018-04-28 18:40:50 UTC  
> Closed at: 2018-04-28 18:40:50 UTC  
> Url: https://github.com/boostorg/config/pull/224  

Expressions like `BOOST_CUDA_VERSION / 1000000` don't work correctly; add parentheses around the expression to fix.

---

## Comment 1

> Username: yuyi1005  
> Created_at: 2018-05-17 09:30:18 UTC  
> Updated_at: 2018-05-17 09:31:09 UTC  
> Url: https://github.com/boostorg/config/pull/224#issuecomment-389805301  

I use vs2017.4+cuda9.1+boost1.67  
When including a boost header file in .cu file, it cannot compile.   
  
fatal error C1012: parentheses not match  
  
in file "boost/preprocessor/slot/detail/shared.hpp"  
line 27:  
if (BOOST_PP_VALUE) / 1000000000UL == 0  
  
Do you have any idea to cope with this issue? Thanks

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-05-17 17:10:35 UTC  
> Url: https://github.com/boostorg/config/pull/224#issuecomment-389940608  

We would need more information - Boost.Preprocessor is included by other libraries to handle preprocessor meta-programs.  In any event, this is not a Boost.Config issue.

---
