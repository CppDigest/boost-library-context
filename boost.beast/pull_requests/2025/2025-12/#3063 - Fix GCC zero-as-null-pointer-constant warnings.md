# #3063 Fix GCC zero-as-null-pointer-constant warnings [Open]

> Username: VemundH  
> Created at: 2025-12-15 16:14:43 UTC  
> Updated at: 2026-01-28 09:39:34 UTC  
> Url: https://github.com/boostorg/beast/pull/3063  

Probably a GCC bug as I have boost as system include. Maybe related to using C++20 coroutines.

---

## Comment 1

> Username: ashtum  
> Created_at: 2025-12-15 16:21:30 UTC  
> Url: https://github.com/boostorg/beast/pull/3063#issuecomment-3656510428  

Do you compile with the `-Wzero-as-null-pointer-constant` flag? If so, there may be more occurrences of this warning in the codebase.

---

## Comment 2

> Username: VemundH  
> Created_at: 2025-12-16 13:19:18 UTC  
> Url: https://github.com/boostorg/beast/pull/3063#issuecomment-3660489060  

> Do you compile with the `-Wzero-as-null-pointer-constant` flag? If so, there may be more occurrences of this warning in the codebase.  
  
I do, but since I include all dependencies as system includes (-isystem) most compiler warnings are normally not reported.  
  
In this PR I fixed all I found related to enable_if. The library already uses nullptr in with enable_if. E.g.:   
https://github.com/boostorg/beast/blob/67106ebaab7a644a9bdb5d6e6b0a3f4ad1d0e4f7/include/boost/beast/core/buffers_generator.hpp#L89

---

## Comment 3

> Username: ashtum  
> Created_at: 2026-01-28 09:39:34 UTC  
> Url: https://github.com/boostorg/beast/pull/3063#issuecomment-3810125890  

Sorry for the late reply.  
Just to make sure I understand, are you saying you’re still seeing warnings even when using `-isystem`, or that your PR is mainly about keeping things consistent?

---
