# #271 Swap include guards to `#ifndef` [Merged]

> Username: IncludeGuardian  
> Created at: 2023-04-20 12:48:36 UTC  
> Updated at: 2024-02-25 04:37:09 UTC  
> Merged at: 2024-02-25 04:37:09 UTC  
> Closed at: 2024-02-25 04:37:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/271  

Compilers implement a multiple-include optimization where they avoid preprocessing an included file if it is "properly guarded". Compilers differ as to what constitutes a "properly guarded" file. In particular, Microsoft Visual Studio does not recognize `#if !defined(GUARD)` for the optimization. This does not affect the correctness of using Fusion on Microsoft Visual Studio, but it will increase the time taken to build any project on this compiler.  
  
This commit changes all include guards to use `#ifndef`, but leaves all other `#if !defined` preprocessor directives that are not related to guarding a file.

---

## Comment 1

> Username: IncludeGuardian  
> Created_at: 2023-04-20 12:55:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/271#issuecomment-1516278538  

This was found investigating the multiple-include optimizations on different compilers.   
 The results can be found here https://github.com/IncludeGuardian/multiple-inclusion-optimization-tests

---

## Review 2 [Approved]

> Username: Kojoley  
> Created_at: 2024-02-24 06:14:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/fusion/pull/271#pullrequestreview-1899225577  

This gives a noticeable parsing speed-up on msvc (8% for `boost/fusion/container.hpp`).

---
