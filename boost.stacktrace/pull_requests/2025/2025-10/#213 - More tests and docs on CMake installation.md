# #213 More tests and docs on CMake installation [Merged]

> Username: apolukhin  
> Created at: 2025-10-08 18:15:28 UTC  
> Updated at: 2025-12-24 17:05:49 UTC  
> Merged at: 2025-12-24 17:05:46 UTC  
> Closed at: 2025-12-24 17:05:46 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/213  



---

## Comment 1

> Username: coveralls  
> Created_at: 2025-10-08 18:45:33 UTC  
> Updated_at: 2025-12-24 11:14:58 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/213#issuecomment-3382788514  

[![Coverage Status](https://coveralls.io/builds/77093695/badge)](https://coveralls.io/builds/77093695)  
  
coverage: 86.35%. remained the same  
when pulling **29b4c8f888a9ae8d3fe63d5e5d230a86fc4e587d on extensive-cmake-install-testing**  
into **b36c55b3d9fc14c4dccb488cff7e7b06c00b6392 on develop**.

---

## Comment 2

> Username: rbberger  
> Created_at: 2025-10-23 23:38:47 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/213#issuecomment-3439815486  

The current CI failures match what I'm observing in my projects. `Boost::stacktrace` isn't defined, although the documentation claims:  
> CMake library Boost::stacktrace provides the best available implementation:

---
