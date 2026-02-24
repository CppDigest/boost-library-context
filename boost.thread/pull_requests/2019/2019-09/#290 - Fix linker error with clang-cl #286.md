# #290 Fix linker error with clang-cl #286 [Merged]

> Username: thebrandre  
> Created at: 2019-09-08 12:49:35 UTC  
> Updated at: 2021-06-05 07:38:26 UTC  
> Merged at: 2019-09-14 13:05:03 UTC  
> Closed at: 2019-09-14 13:05:03 UTC  
> Url: https://github.com/boostorg/thread/pull/290  

Fix issue #286  
  
Analog to this [patch for boost.type_index](https://github.com/boostorg/type_index/pull/25/commits), I check for clang-win by testing if both `__clang__` and `_MSC_VER` are set.

---
