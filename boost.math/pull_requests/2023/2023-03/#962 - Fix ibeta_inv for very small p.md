# #962 Fix ibeta_inv for very small p. [Merged]

> Username: jzmaddock  
> Created at: 2023-03-04 19:24:27 UTC  
> Updated at: 2024-01-24 21:33:49 UTC  
> Merged at: 2023-03-05 13:18:27 UTC  
> Closed at: 2023-03-05 13:18:27 UTC  
> Url: https://github.com/boostorg/math/pull/962  

Change assert's in temme_method_1_ibeta_inverse to corrections when guess goes out of range. Change handling of non-convergence in second_order_root_finder to use bracketing when the end points are many orders of magnitude apart. Fixes: https://github.com/boostorg/math/issues/961.

---

## Comment 1

> Username: WarrenWeckesser  
> Created_at: 2023-03-04 20:09:11 UTC  
> Url: https://github.com/boostorg/math/pull/962#issuecomment-1454860698  

This fixes the problem with the code that I included in #961. :+1:

---
