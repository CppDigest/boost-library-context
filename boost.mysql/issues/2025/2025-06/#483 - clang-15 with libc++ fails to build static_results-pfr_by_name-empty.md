# #483 - clang-15 with libc++ fails to build static_results<pfr_by_name<empty>> [Closed]

> Username: anarthal  
> Created at: 2025-06-03 18:13:43 UTC  
> Updated at: 2025-06-07 11:20:49 UTC  
> Closed at: 2025-06-07 11:20:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/483  

[This check](https://github.com/boostorg/mysql/blob/90405e79e4590f142e28094eea1625f259ffeec4/test/unit/test/pfr.cpp) fails under recent versions of AppleClang (see [this build](https://github.com/boostorg/mysql/actions/runs/15423871327/job/43405774053?pr=480)). Understand why and report any potential problem to PFR.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-06-07 11:18:55 UTC  
> Updated at: 2025-06-07 11:19:08 UTC  
> Url: https://github.com/boostorg/mysql/issues/483#issuecomment-2952370431  

This is due to a recent PFR change that fixes https://github.com/boostorg/pfr/issues/165. Older clang/libc++ versions have problems compiling `names_as_array` in a constexpr context when the input type is empty.
