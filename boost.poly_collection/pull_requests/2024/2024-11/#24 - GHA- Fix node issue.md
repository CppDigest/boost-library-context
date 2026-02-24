# #24 GHA: Fix node issue [Closed]

> Username: Flamefire  
> Created at: 2024-11-26 17:33:48 UTC  
> Updated at: 2024-11-29 16:44:51 UTC  
> Closed at: 2024-11-29 16:27:57 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/24  

Github Actions will force the use of node20 which doesn't work in the containers. This env variable extends the period where node16 is usable

---

## Comment 1

> Username: joaquintides  
> Created_at: 2024-11-29 16:27:57 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/24#issuecomment-2508115315  

This PR doesn't fix the problem. Solved after your further investigations at https://github.com/boostorg/poly_collection/tree/feature/variant_collection

---
