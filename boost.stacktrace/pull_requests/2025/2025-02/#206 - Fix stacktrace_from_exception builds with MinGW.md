# #206 Fix stacktrace_from_exception builds with MinGW [Merged]

> Username: apolukhin  
> Created at: 2025-02-18 06:20:47 UTC  
> Updated at: 2026-02-11 16:33:48 UTC  
> Merged at: 2025-02-18 10:41:41 UTC  
> Closed at: 2025-02-18 10:41:41 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/206  

Fixes https://github.com/boostorg/stacktrace/issues/196

---

## Comment 1

> Username: coveralls  
> Created_at: 2025-02-18 06:50:25 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/206#issuecomment-2664757943  

[![Coverage Status](https://coveralls.io/builds/72287852/badge)](https://coveralls.io/builds/72287852)  
  
coverage: 86.371%. remained the same  
when pulling **0f3e6f2e55d8469e9573aed7c4f025f269095245 on apolukhin-patch-3**  
into **d408cf446875dcfaf81f37169908f5f1bd01a5d2 on develop**.

---

## Comment 2

> Username: orgads  
> Created_at: 2026-02-11 16:33:48 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/206#issuecomment-3885575375  

This breaks cross-build. See https://github.com/mxe/mxe/issues/3281.

---
