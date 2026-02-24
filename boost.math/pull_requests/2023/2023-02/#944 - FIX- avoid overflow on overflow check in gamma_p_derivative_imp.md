# #944 FIX: avoid overflow on overflow check in gamma_p_derivative_imp [Closed]

> Username: mckib2  
> Created at: 2023-02-05 05:44:31 UTC  
> Updated at: 2023-02-05 05:48:29 UTC  
> Closed at: 2023-02-05 05:48:28 UTC  
> Url: https://github.com/boostorg/math/pull/944  

- x-ref https://github.com/scipy/scipy/pull/17432  
- Same rationale/fix as https://github.com/boostorg/math/pull/937  
- avoid calculating `[number]*tools::max_value<T>()` to prevent overflows on Apple M1 processors

---

## Comment 1

> Username: mckib2  
> Created_at: 2023-02-05 05:48:28 UTC  
> Url: https://github.com/boostorg/math/pull/944#issuecomment-1416929055  

Superceded by https://github.com/boostorg/math/pull/945

---
