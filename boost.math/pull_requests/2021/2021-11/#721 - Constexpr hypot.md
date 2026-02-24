# #721 Constexpr hypot [Merged]

> Username: mborland  
> Created at: 2021-11-25 13:39:18 UTC  
> Updated at: 2021-11-28 10:15:29 UTC  
> Merged at: 2021-11-27 23:33:11 UTC  
> Closed at: 2021-11-27 23:33:11 UTC  
> Url: https://github.com/boostorg/math/pull/721  



---

## Comment 1

> Username: mborland  
> Created_at: 2021-11-27 17:12:57 UTC  
> Url: https://github.com/boostorg/math/pull/721#issuecomment-980721005  

@NAThompson This should be good to go.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2021-11-27 23:32:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/721#pullrequestreview-817161976  

📁 include/boost/math/ccmath/detail/swap.hpp

```diff
  15 |+     x = y;
  16 |+     y = temp;
  17 |+ }
```

> Username: NAThompson  
> Created_at: 2021-11-27 23:32:16 UTC  
> Url: https://github.com/boostorg/math/pull/721#discussion_r757821718  

@mborland : Is there no constexpr `std::swap`?

> Username: mborland  
> Created_at: 2021-11-28 10:15:28 UTC  
> Updated_at: 2021-11-28 10:15:29 UTC  
> Url: https://github.com/boostorg/math/pull/721#discussion_r757873739  

`std::swap` only became `constexpr` in [C++20.](https://en.cppreference.com/w/cpp/algorithm/swap)


---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-11-27 23:33:08 UTC  
> Url: https://github.com/boostorg/math/pull/721#issuecomment-980805919  

@mborland : If you do find a `constexpr std::swap` just open up another PR. I figure you probably checked this . . .

---
