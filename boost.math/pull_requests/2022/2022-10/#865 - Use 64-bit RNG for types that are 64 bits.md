# #865 Use 64-bit RNG for types that are 64 bits [Merged]

> Username: SiliconA-Z  
> Created at: 2022-10-31 15:42:59 UTC  
> Updated at: 2022-11-04 16:20:52 UTC  
> Merged at: 2022-11-04 16:20:52 UTC  
> Closed at: 2022-11-04 16:20:52 UTC  
> Url: https://github.com/boostorg/math/pull/865  

Do this instead of casting down every time we do a normal distribution.

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2022-10-31 20:03:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/865#pullrequestreview-1162480346  

📁 include/boost/math/tools/random_vector.hpp

```diff
  30 |     std::normal_distribution<T> dis(0, 1);
  31 |-     for(std::size_t i = 0; i < v.size(); ++i)
  31 |+     for (auto& i : v)
```

> Username: NAThompson  
> Created_at: 2022-10-31 20:03:40 UTC  
> Url: https://github.com/boostorg/math/pull/865#discussion_r1009817998  

I might use `auto& x : v` as we generally will assume `dis(gen)` produces a floating point number.

> Username: SiliconA-Z  
> Created_at: 2022-11-01 00:47:33 UTC  
> Url: https://github.com/boostorg/math/pull/865#discussion_r1009982631  

Done!


---
