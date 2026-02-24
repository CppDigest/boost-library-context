# #438 Update iohb.c to use snprintf [Merged]

> Username: Becheler  
> Created at: 2025-08-24 19:18:50 UTC  
> Updated at: 2026-01-12 12:21:56 UTC  
> Merged at: 2025-08-25 05:39:14 UTC  
> Closed at: 2025-08-25 05:39:14 UTC  
> Url: https://github.com/boostorg/graph/pull/438  

Use snprintf if available as some compilers (clang 14.0) issue deprecation warnings for sprintf, see issue #437

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-08-25 05:21:04 UTC  
> Url: https://github.com/boostorg/graph/pull/438#issuecomment-3218870389  

We have an example in C???

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2025-08-25 05:39:01 UTC  
> Url: https://github.com/boostorg/graph/pull/438#issuecomment-3218900562  

Although some of those checks failed/cancelled for weird reasons, the important checks passed.

---
