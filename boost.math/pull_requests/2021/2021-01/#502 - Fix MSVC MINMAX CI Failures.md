# #502 Fix MSVC MINMAX CI Failures [Merged]

> Username: mborland  
> Created at: 2021-01-26 16:35:34 UTC  
> Updated at: 2021-01-28 16:28:41 UTC  
> Merged at: 2021-01-28 16:26:54 UTC  
> Closed at: 2021-01-28 16:26:55 UTC  
> Url: https://github.com/boostorg/math/pull/502  

Many of these errors continue to stem from https://github.com/boostorg/random/pull/76 not being resolved.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-01-27 17:20:11 UTC  
> Url: https://github.com/boostorg/math/pull/502#issuecomment-768441915  

@jzmaddock This gets us back to green.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-01-27 17:46:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/502#pullrequestreview-577601036  

📁 test/test_carlson.hpp

```diff
  13 |+ #ifdef max
  14 |+ #undef max
  15 |+ #endif
```

> Username: jzmaddock  
> Created_at: 2021-01-27 17:46:33 UTC  
> Updated_at: 2021-01-27 18:37:34 UTC  
> Url: https://github.com/boostorg/math/pull/502#discussion_r565509471  

Point of pedantry: isn't this superfluous now?

> Username: mborland  
> Created_at: 2021-01-27 18:36:36 UTC  
> Updated_at: 2021-01-27 18:37:34 UTC  
> Url: https://github.com/boostorg/math/pull/502#discussion_r565543237  

You are correct.


---

## Comment 3

> Username: mborland  
> Created_at: 2021-01-28 15:26:11 UTC  
> Url: https://github.com/boostorg/math/pull/502#issuecomment-769162140  

@jzmaddock This is good with the superfluous macros removed. I will open a PR with random to hit all the minmax violations.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-01-28 16:26:48 UTC  
> Url: https://github.com/boostorg/math/pull/502#issuecomment-769204430  

Thanks Matt!

---
