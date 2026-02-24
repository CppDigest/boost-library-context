# #734 Constexpr fma [Merged]

> Username: mborland  
> Created at: 2022-01-06 21:52:50 UTC  
> Updated at: 2022-05-31 15:04:33 UTC  
> Merged at: 2022-05-30 20:35:54 UTC  
> Closed at: 2022-05-30 20:35:55 UTC  
> Url: https://github.com/boostorg/math/pull/734  

Implements: `fma`, `fmaf`, and `fmal`

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-01-07 09:13:37 UTC  
> Url: https://github.com/boostorg/math/pull/734#issuecomment-1007250747  

On the one hand this looks trivial.... on the other hand do we not run the risk of compile-time and runtime fma's returning subtly differing values?

---

## Comment 2

> Username: mborland  
> Created_at: 2022-01-07 12:08:58 UTC  
> Url: https://github.com/boostorg/math/pull/734#issuecomment-1007357640  

If the compiler doesn't optimize this to an `FMA` asm call and the builtin is not available then you are correct that it could be off by one rounding. C++20 allows for constexpr asm if you would prefer that I hard code it for major architectures.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2022-01-08 06:52:18 UTC  
> Url: https://github.com/boostorg/math/pull/734#issuecomment-1007898141  

@jzmaddock : Is an fma for multiprecision infeasible or just a PITA?

---

## Comment 4

> Username: mborland  
> Created_at: 2022-05-28 15:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/734#issuecomment-1140281361  

@NAThompson This is clean and good for review. The implementation is trivial, but with the documented compiler flag the results are exact. [Here](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/detail/default_ops.hpp#L3107) is the prototype for ima support in Boost.Multiprecision.

---

## Comment 5

> Username: mborland  
> Created_at: 2022-05-28 15:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/734#issuecomment-1140281362  

@NAThompson This is clean and good for review. The implementation is trivial, but with the documented compiler flag the results are exact. [Here](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/detail/default_ops.hpp#L3107) is the prototype for ima support in Boost.Multiprecision.

---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2022-05-30 01:18:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/734#pullrequestreview-988696205  

📁 include/boost/math/ccmath/fma.hpp

```diff
  37 |+     
  38 |+     // If we can't use compiler intrinsics hope that -fma flag optimizes this call to fma instruction
  39 |+     return (x * y) + z;
```

> Username: NAThompson  
> Created_at: 2022-05-30 01:18:13 UTC  
> Url: https://github.com/boostorg/math/pull/734#discussion_r884356658  

So std::fma won't work here?  
  
Also, why not just place the asm directly in the code if we detect x86 >= ivy bridge?

> Username: mborland  
> Created_at: 2022-05-30 16:34:32 UTC  
> Url: https://github.com/boostorg/math/pull/734#discussion_r884978930  

`std::fma` is likely not constexpr until C++23. [According to this accepted C++20 proposal](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1668r1.html) inlining asm is not needed if `std::is_constant_evaluated` is true


---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2022-05-30 01:19:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/734#pullrequestreview-988696696  

📁 include/boost/math/ccmath/fma.hpp

```diff
  47 |+     if (BOOST_MATH_IS_CONSTANT_EVALUATED(x))
  48 |+     {
  49 |+         if (x == 0 && boost::math::ccmath::isinf(y))
```

> Username: NAThompson  
> Created_at: 2022-05-30 01:19:51 UTC  
> Url: https://github.com/boostorg/math/pull/734#discussion_r884357002  

Is this not a bit too branchy here? I worry about the case where x is constant evaluated but y and z are runtime and this becomes slow as a result.

> Username: mborland  
> Created_at: 2022-05-30 16:37:18 UTC  
> Url: https://github.com/boostorg/math/pull/734#discussion_r884980306  

x is only a place holder for old versions (GCC 6-8) of gcc's builtin constant evaluated. On GCC 9+ and MSVC this compiles down to `std::is_constant_evaluated()`. Either the whole thing can be constant evaluated or none of it.


---

## Comment 8

> Username: NAThompson  
> Created_at: 2022-05-30 17:58:47 UTC  
> Url: https://github.com/boostorg/math/pull/734#issuecomment-1141383675  

Cool; this looks good. Is the failed job a known issue?

---

## Comment 9

> Username: mborland  
> Created_at: 2022-05-30 19:11:51 UTC  
> Url: https://github.com/boostorg/math/pull/734#issuecomment-1141423887  

> Cool; this looks good. Is the failed job a known issue?  
  
It's a sanitizer issue in autodiff that seems to be popping up consistently.

---
