# #116 Update boost_no_cxx11_sfinae_expr test to fail faster [Merged]

> Username: glenfe  
> Created at: 2017-02-07 23:22:24 UTC  
> Updated at: 2017-02-20 14:59:46 UTC  
> Merged at: 2017-02-08 18:30:49 UTC  
> Closed at: 2017-02-08 18:30:49 UTC  
> Url: https://github.com/boostorg/config/pull/116  

Will now compile-fail for Intel C++ 13 instead of run-fail.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-02-20 09:00:22 UTC  
> Url: https://github.com/boostorg/config/pull/116#issuecomment-281022402  

This is failing for almost all clang test runners, can you investigate?  
  
Generally I tend to assume that clang is correct... but you never know.

---

## Comment 2

> Username: glenfe  
> Created_at: 2017-02-20 14:59:46 UTC  
> Url: https://github.com/boostorg/config/pull/116#issuecomment-281100785  

That's clang in C++03 mode (i.e. with -std=c++11 or higher you won't see those errors). The test code uses C++11. I'll submit a pull request shortly.

---
