# #937 FIX: avoid overflow on overflow check in halley_step on Apple M1 [Closed]

> Username: mckib2  
> Created at: 2023-01-31 06:58:19 UTC  
> Updated at: 2023-02-05 05:48:04 UTC  
> Closed at: 2023-02-05 05:48:04 UTC  
> Url: https://github.com/boostorg/math/pull/937  

- x-ref https://github.com/scipy/scipy/pull/17432  
- avoid calculating `[number]*tools::max_value<T>()` to prevent overflows on Apple M1 precessors

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-01-31 14:08:17 UTC  
> Url: https://github.com/boostorg/math/pull/937#issuecomment-1410418566  

I'm sure I'm missing something obvious here, but if denom >=1 there can be no overflow, and if its < 1 then denom*numeric_limits<>::max() can't overflow either?  Or is a logical branch which should never be taken being speculatively evaluated?

---

## Comment 2

> Username: mckib2  
> Created_at: 2023-01-31 15:07:51 UTC  
> Url: https://github.com/boostorg/math/pull/937#issuecomment-1410531056  

>  Or is a logical branch which should never be taken being speculatively evaluated?  
  
That's what I thought at first because we have seen that before on Apple Silicon, but splitting the expression out into two if statements (which resolved this previously) didn't resolve it locally for me and I don't know of another way to prevent the speculative execution.  So if it might be executed, the change in this PR was the only thing I could find that avoided the `FE_OVERFLOW` on an M1 Mac Mini

---

## Review 3 [Commented]

> Username: mborland  
> Created_at: 2023-02-04 15:49:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/937#pullrequestreview-1284082266  

📁 include/boost/math/tools/roots.hpp

```diff
 359 |+          // RHS may overflow on Apple M1, so rearrange:
 360 |+          //     |denom| * 1/max_value >= |num|
 361 |+          constexpr T inv_max_value = 1.0 / tools::max_value<T>();
```

> Username: mborland  
> Created_at: 2023-02-04 15:48:42 UTC  
> Updated_at: 2023-02-04 15:49:02 UTC  
> Url: https://github.com/boostorg/math/pull/937#discussion_r1096552001  

```diff  
-         constexpr T inv_max_value = 1.0 / tools::max_value<T>();  
+         const T inv_max_value = 1.0 / tools::max_value<T>();  
```

> Username: jzmaddock  
> Created_at: 2023-02-04 18:31:02 UTC  
> Url: https://github.com/boostorg/math/pull/937#discussion_r1096570900  

Even better make that `static const` and then it's evaluated just the once.

> Username: mckib2  
> Created_at: 2023-02-05 05:12:28 UTC  
> Updated_at: 2023-02-05 05:12:29 UTC  
> Url: https://github.com/boostorg/math/pull/937#discussion_r1096624567  

I wanted `static constexpr`, but wasn't sure that is supported everywhere we want, and indeed, it appears even `constexpr` is problematic.  Will implement this right away


---

## Comment 4

> Username: mckib2  
> Created_at: 2023-02-05 05:48:04 UTC  
> Url: https://github.com/boostorg/math/pull/937#issuecomment-1416929007  

Superceded by https://github.com/boostorg/math/pull/945

---
