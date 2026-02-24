# #1135 Correct float_next(+INF) and float_prior(-INF) [Merged]

> Username: jzmaddock  
> Created at: 2024-05-17 18:17:33 UTC  
> Updated at: 2024-06-05 09:10:25 UTC  
> Merged at: 2024-05-18 08:21:42 UTC  
> Closed at: 2024-05-18 08:21:42 UTC  
> Url: https://github.com/boostorg/math/pull/1135  

Fixes https://github.com/boostorg/math/issues/1132

---

## Comment 1

> Username: tchaikov  
> Created_at: 2024-05-17 23:51:36 UTC  
> Updated_at: 2024-05-17 23:51:56 UTC  
> Url: https://github.com/boostorg/math/pull/1135#issuecomment-2118500230  

thank you. IIUC, once this change lands, we should revert https://github.com/boostorg/multiprecision/pull/618, right? as it is basically a work around of this issue.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-05-18 08:21:38 UTC  
> Url: https://github.com/boostorg/math/pull/1135#issuecomment-2118691195  

One failure is a network timeout... merging.

---

## Review 3 [Commented]

> Username: jwakely  
> Created_at: 2024-06-05 09:10:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1135#pullrequestreview-2098501813  

📁 include/boost/math/special_functions/next.hpp

```diff
 205 |       return policies::raise_domain_error<T>(
 206 |          function,
 207 |          "Argument must be finite, but got %1%", val, pol);
```

> Username: jwakely  
> Created_at: 2024-06-05 09:10:25 UTC  
> Url: https://github.com/boostorg/math/pull/1135#discussion_r1627324850  

These error messages are no longer really accurate, the argument can be an infinity which is of course not finite.


---
