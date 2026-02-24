# #34 Fixed recursion in extract_value_type [Closed]

> Username: Kojoley  
> Created at: 2019-03-31 20:46:47 UTC  
> Updated at: 2023-12-15 12:58:44 UTC  
> Closed at: 2023-12-15 12:58:43 UTC  
> Url: https://github.com/boostorg/odeint/pull/34  

Some types (like Boost.Multiprecision ones) define `value_type` to the same type as they are, what causes `extract_value_type` instantiation to fail with an incomplete type instantiation error.  
  
Fixes tests:  
* `runge_kutta_concepts`  
* `runge_kutta_controlled_concepts`  
* `runge_kutta_error_concepts`

---

## Comment 1

> Username: mborland  
> Created_at: 2023-12-15 12:58:44 UTC  
> Url: https://github.com/boostorg/odeint/pull/34#issuecomment-1857845296  

Fix pulled into: https://github.com/boostorg/odeint/pull/63

---
