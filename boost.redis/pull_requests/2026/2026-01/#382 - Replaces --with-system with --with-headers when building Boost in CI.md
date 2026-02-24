# #382 Replaces --with-system with --with-headers when building Boost in CI [Merged]

> Username: anarthal  
> Created at: 2026-01-18 14:21:14 UTC  
> Updated at: 2026-01-18 14:44:59 UTC  
> Merged at: 2026-01-18 14:44:56 UTC  
> Closed at: 2026-01-18 14:44:56 UTC  
> Url: https://github.com/boostorg/redis/pull/382  



---

## Comment 1

> Username: anarthal  
> Created_at: 2026-01-18 14:21:58 UTC  
> Url: https://github.com/boostorg/redis/pull/382#issuecomment-3765347735  

Aims at fixing a recent failure. We're using --with-system to tell b2 to only install headers (and not build binaries), but this no longer works. --with-headers is the right way.

---
