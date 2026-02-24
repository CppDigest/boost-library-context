# #316 __cpp_sized_deallocation is not always defined (clang is opt-in) for Issue #306 [Merged]

> Username: nigels-com  
> Created at: 2025-09-05 06:02:47 UTC  
> Updated at: 2025-09-06 10:20:15 UTC  
> Merged at: 2025-09-06 10:20:07 UTC  
> Closed at: 2025-09-06 10:20:07 UTC  
> Url: https://github.com/boostorg/container/pull/316  

In reference to Issue #306   
  
I noticed on Ubuntu 24.04 that gcc defines `__cpp_sized_deallocation` but clang does not, unless [opted into](https://bcain-llvm.readthedocs.io/projects/clang/en/release_37/ReleaseNotes/#new-compiler-flags).  
  
So I agree that these checks should be refined to `#if defined()`

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2025-09-06 10:20:15 UTC  
> Url: https://github.com/boostorg/container/pull/316#issuecomment-3261765616  

Many thanks for the patch!

---
