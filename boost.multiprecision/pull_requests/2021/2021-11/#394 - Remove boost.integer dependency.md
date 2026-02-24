# #394 Remove boost.integer dependency [Merged]

> Username: mborland  
> Created at: 2021-11-22 19:23:33 UTC  
> Updated at: 2021-11-26 09:11:33 UTC  
> Merged at: 2021-11-26 09:11:32 UTC  
> Closed at: 2021-11-26 09:11:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/394  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-11-22 19:38:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/394#pullrequestreview-812946894  

📁 include/boost/multiprecision/detail/common_factor.hpp

```diff
  34 |+ 
  35 |+ template <typename T>
  36 |+ inline BOOST_CXX14_CONSTEXPR void constexpr_swap(T& a, T& b) noexcept
```

> Username: jzmaddock  
> Created_at: 2021-11-22 19:38:25 UTC  
> Updated_at: 2021-11-22 19:38:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/394#discussion_r754580919  

I wonder/worry a bit about this - gcd is typically time-critical and binary-gcd is usually faster than Euclid.  We also already have: https://github.com/boostorg/multiprecision/blob/d6b0f2ecd50992e868652806e437991c771dc6b2/include/boost/multiprecision/cpp_int/misc.hpp#L397-L437 and perhaps we should do something similar here and/or share code?


---

## Comment 2

> Username: mborland  
> Created_at: 2021-11-23 18:58:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/394#issuecomment-977019620  

This is clean with the requested changes.

---
