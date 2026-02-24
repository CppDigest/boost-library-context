# #391 Refactors the Sentinel response parser to use flat_tree  [Merged]

> Username: anarthal  
> Created at: 2026-01-27 20:25:03 UTC  
> Updated at: 2026-01-28 10:07:54 UTC  
> Merged at: 2026-01-28 10:07:52 UTC  
> Closed at: 2026-01-28 10:07:52 UTC  
> Url: https://github.com/boostorg/redis/pull/391  

The code now uses checked access via `flat_tree::at`, for hardening.

---

## Comment 1

> Username: anarthal  
> Created_at: 2026-01-27 20:25:18 UTC  
> Url: https://github.com/boostorg/redis/pull/391#issuecomment-3807371928  

Entails no functional change.

---

## Comment 2

> Username: anarthal  
> Created_at: 2026-01-28 10:07:22 UTC  
> Url: https://github.com/boostorg/redis/pull/391#issuecomment-3810290012  

Merging as per Marcelo's request.

---
