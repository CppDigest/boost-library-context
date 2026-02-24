# #2 Use _aligned_malloc for all MSVC-compatible compilers [Merged]

> Username: Lastique  
> Created at: 2014-05-04 10:01:22 UTC  
> Updated at: 2014-05-04 14:33:24 UTC  
> Merged at: 2014-05-04 14:29:52 UTC  
> Closed at: 2014-05-04 14:29:52 UTC  
> Url: https://github.com/boostorg/align/pull/2  

Any compiler pretending to be MSVC should implement _aligned_malloc/_aligned_free. In particular this includes Intel Compiler on Windows and (in the future) Clang. These compilers reuse MSVC CRT, which implements these functions.

---

## Comment 1

> Username: glenfe  
> Created_at: 2014-05-04 14:33:24 UTC  
> Url: https://github.com/boostorg/align/pull/2#issuecomment-42134268  

Merged **a9cd8f6** from **`Lastique:patch-1`** into **`boostorg:develop`**.

---
