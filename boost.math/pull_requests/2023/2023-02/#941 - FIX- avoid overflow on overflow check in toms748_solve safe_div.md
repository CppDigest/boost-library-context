# #941  FIX: avoid overflow on overflow check in toms748_solve safe_div [Closed]

> Username: mckib2  
> Created at: 2023-02-04 03:44:33 UTC  
> Updated at: 2023-02-05 05:48:19 UTC  
> Closed at: 2023-02-05 05:48:18 UTC  
> Url: https://github.com/boostorg/math/pull/941  

- x-ref https://github.com/scipy/scipy/pull/17432  
- Same rationale/fix as https://github.com/boostorg/math/pull/937  
- avoid calculating `[number]*tools::max_value<T>()` to prevent overflows on Apple M1 processors

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2023-02-04 15:48:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/941#pullrequestreview-1284082202  

📁 include/boost/math/tools/toms748_solve.hpp

```diff
 162 |    {
 163 |-       if(fabs(denom * tools::max_value<T>()) <= fabs(num))
 163 |+       constexpr T inv_max_value = 1.0 / tools::max_value<T>();
```

> Username: mborland  
> Created_at: 2023-02-04 15:48:01 UTC  
> Updated_at: 2023-02-04 15:48:15 UTC  
> Url: https://github.com/boostorg/math/pull/941#discussion_r1096551910  

```diff  
-      constexpr T inv_max_value = 1.0 / tools::max_value<T>();  
+      const T inv_max_value = 1.0 / tools::max_value<T>();  
```  
  
As @jzmaddock noted in https://github.com/boostorg/math/pull/942 we still need to support types that don't have constexpr construction.

> Username: jzmaddock  
> Created_at: 2023-02-04 18:31:32 UTC  
> Url: https://github.com/boostorg/math/pull/941#discussion_r1096570981  

Even better make that `static const` and then it's evaluated just the once.

> Username: mckib2  
> Created_at: 2023-02-05 05:17:28 UTC  
> Url: https://github.com/boostorg/math/pull/941#discussion_r1096624878  

Implemented


---

## Comment 2

> Username: mckib2  
> Created_at: 2023-02-05 05:48:18 UTC  
> Url: https://github.com/boostorg/math/pull/941#issuecomment-1416929032  

Superceded by https://github.com/boostorg/math/pull/945

---
