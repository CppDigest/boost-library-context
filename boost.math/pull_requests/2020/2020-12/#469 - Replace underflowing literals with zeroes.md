# #469 Replace underflowing literals with zeroes [Merged]

> Username: evanmiller  
> Created at: 2020-12-23 18:58:25 UTC  
> Updated at: 2021-01-13 14:31:22 UTC  
> Merged at: 2021-01-13 14:31:21 UTC  
> Closed at: 2021-01-13 14:31:21 UTC  
> Url: https://github.com/boostorg/math/pull/469  

GCC emits a large number of warnings when `-Woverflow` is enabled. Replace floating point literals that are less than 1e-10000 (roughly the square of the smallest representable number in quad precision) with zero in the hypergeometric 1f1 regularized table.  
  
Tests are passing locally. See #237 for more context.

---

## Comment 1

> Username: evanmiller  
> Created_at: 2021-01-13 14:26:58 UTC  
> Url: https://github.com/boostorg/math/pull/469#issuecomment-759483337  

Gently bumping before this goes stale (@jzmaddock)

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-01-13 14:31:16 UTC  
> Url: https://github.com/boostorg/math/pull/469#issuecomment-759486059  

Forgot about that one - thanks for the reminder - merging now!

---
