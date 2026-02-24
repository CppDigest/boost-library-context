# #532 Remove MPL from Lanczos [Merged]

> Username: mborland  
> Created at: 2021-02-12 17:45:09 UTC  
> Updated at: 2021-02-14 11:26:38 UTC  
> Merged at: 2021-02-13 19:19:26 UTC  
> Closed at: 2021-02-13 19:19:26 UTC  
> Url: https://github.com/boostorg/math/pull/532  

Additionally replace boost::integral_constant and boost/cstdint with standard library types.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-02-13 09:26:22 UTC  
> Url: https://github.com/boostorg/math/pull/532#issuecomment-778589597  

All CI failures are at the CI system's add repository step.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-02-13 11:20:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/532#pullrequestreview-589961646  

📁 include/boost/math/special_functions/lanczos.hpp

```diff
3231 |+                                                                                                    policies::precision<Real, Policy>::type::value;
3232 |+ 
3233 |+    using type = typename std::conditional<(target_precision <= 24), lanczos6m24,
```

> Username: jzmaddock  
> Created_at: 2021-02-13 11:20:00 UTC  
> Updated_at: 2021-02-13 11:34:46 UTC  
> Url: https://github.com/boostorg/math/pull/532#discussion_r575655415  

I'm being super-picky here, but `(target_precision <= lanczos6m24::value)` would be better than copy-and-pasting the values.

> Username: mborland  
> Created_at: 2021-02-13 11:35:36 UTC  
> Updated_at: 2021-02-13 11:35:37 UTC  
> Url: https://github.com/boostorg/math/pull/532#discussion_r575656753  

Magic numbers have been removed in 07144e2.


---
