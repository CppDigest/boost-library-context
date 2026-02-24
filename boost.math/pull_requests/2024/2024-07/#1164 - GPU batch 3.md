# #1164 GPU batch 3 [Merged]

> Username: mborland  
> Created at: 2024-07-26 15:33:25 UTC  
> Updated at: 2024-07-29 19:26:40 UTC  
> Merged at: 2024-07-29 19:26:38 UTC  
> Closed at: 2024-07-29 19:26:38 UTC  
> Url: https://github.com/boostorg/math/pull/1164  

Adds CUDA and SYCL support for `log1p`, `log1pmx`, `digamma`, `trigamma`, `sin_pi`, `cos_pi`, `pow`, and the bernoulli distribution.  
  
Significant changes: Adds a complete implementation of `<limits>` in tools/numeric_limits.hpp. This allows us to work around it missing from libcu++ without giant chains of `std::is_same_v` and `if constexpr`. The other big change is the method of dispatching for `sin_pi` and `trigamma` to avoid recursion that is disallowed by SYCL. `trigamma` also now uses `std::integral_constant` directly rather than through a pointer because that confused NVCC quite badly.

---

## Comment 1

> Username: mborland  
> Created_at: 2024-07-29 19:26:24 UTC  
> Url: https://github.com/boostorg/math/pull/1164#issuecomment-2256732495  

Only failure was the out of memory on drone that is now fixed.

---
