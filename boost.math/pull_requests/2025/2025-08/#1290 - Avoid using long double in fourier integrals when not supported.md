# #1290 Avoid using long double in fourier integrals when not supported [Merged]

> Username: andrjohns  
> Created at: 2025-08-01 07:49:00 UTC  
> Updated at: 2025-08-02 07:37:48 UTC  
> Merged at: 2025-08-01 16:36:40 UTC  
> Closed at: 2025-08-01 16:36:40 UTC  
> Url: https://github.com/boostorg/math/pull/1290  

When initialising a fourier integral class with double precision (e.g., `quadrature::ooura_fourier_sin<double>`) the internals always call the `add_level` member function with `long double`, even if the platform does not support it

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2025-08-01 13:44:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1290#pullrequestreview-3079309522  

This looks fine to me. Any comments @NAThompson?   
  
Unrelated to this PR it seems like the if-else logic being modified here should be `if constexpr`/`BOOST_MATH_IF_CONSTEXPR` since we are branching on the type of `Real`

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-08-01 16:07:10 UTC  
> Url: https://github.com/boostorg/math/pull/1290#issuecomment-3145071833  

+1.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2025-08-01 16:26:33 UTC  
> Url: https://github.com/boostorg/math/pull/1290#issuecomment-3145123486  

+1

---
