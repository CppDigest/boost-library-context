# #179 Fix build on mingw [Closed]

> Username: jschueller  
> Created at: 2024-08-31 08:20:01 UTC  
> Updated at: 2024-09-02 20:15:33 UTC  
> Closed at: 2024-09-02 20:15:33 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/179  

Fixes build on mingw where the posix/dlsym path will fail to build even if a dlfcn wrapper is provided , its simpler to just use the msvc path for all windows targets  
  
/cc @apolukhin from #147

---

## Comment 1

> Username: coveralls  
> Created_at: 2024-09-01 14:51:12 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/179#issuecomment-2323376158  

[![Coverage Status](https://coveralls.io/builds/69545626/badge)](https://coveralls.io/builds/69545626)  
  
coverage: 86.35%. remained the same  
when pulling **1eb805dec2abecf041752bbedfabc339bfad92b6 on jschueller:patch-2**  
into **cbf0df774dd7baa2af5a7d28662fd58b90ea8c3d on boostorg:develop**.

---
