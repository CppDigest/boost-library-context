# #819 Improve ccmath signbit and copysign [Merged]

> Username: mborland  
> Created at: 2022-08-19 02:36:52 UTC  
> Updated at: 2022-08-26 16:28:27 UTC  
> Merged at: 2022-08-26 16:28:23 UTC  
> Closed at: 2022-08-26 16:28:23 UTC  
> Url: https://github.com/boostorg/math/pull/819  

Use `std::bit_cast` or `__builtin_bit_cast` when available to support signed zeros and NANs.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-08-21 22:48:43 UTC  
> Url: https://github.com/boostorg/math/pull/819#issuecomment-1221635415  

@jzmaddock and @NAThompson this is good for review. It conditionally solves the issue of not supporting 0s and NANs brought up on the mailing list.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2022-08-22 18:30:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/819#pullrequestreview-1080970315  

📁 include/boost/math/ccmath/signbit.hpp

```diff
  16 |+ #include <boost/math/ccmath/abs.hpp>
  17 |+ 
  18 |+ #if __cpp_lib_bit_cast >= 201806L
```

> Username: jzmaddock  
> Created_at: 2022-08-22 18:30:33 UTC  
> Url: https://github.com/boostorg/math/pull/819#discussion_r951772954  

I think we need to include <version> before checking for this macro (and use __has_include to check for <version>).


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2022-08-22 18:34:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/819#pullrequestreview-1080974493  

📁 include/boost/math/ccmath/signbit.hpp

```diff
  40 |+ */
  41 |+ 
  42 |+ #if defined(__clang__) && defined(BOOST_MATH_BIT_CAST)
```

> Username: jzmaddock  
> Created_at: 2022-08-22 18:34:33 UTC  
> Url: https://github.com/boostorg/math/pull/819#discussion_r951776856  

Is this all clang's or just Apples?  
  
There is an alternative implementation which avoids bit fields: just store the left shift which puts the sign bit at index 0, then bit_cast to an appropriately sized integer, shift and bitwise & with 1u.

> Username: mborland  
> Created_at: 2022-08-23 01:34:18 UTC  
> Updated_at: 2022-08-23 01:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/819#discussion_r952059282  

I just ran clang 12,13, and 14 on Ubuntu. Looks like it is not strictly an Apple problem.


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2022-08-22 18:35:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/819#pullrequestreview-1080975655  

📁 include/boost/math/ccmath/signbit.hpp

```diff
 170 |+ 
 171 |+ #else
 172 |+ 
```

> Username: jzmaddock  
> Created_at: 2022-08-22 18:35:38 UTC  
> Updated_at: 2022-08-22 18:35:39 UTC  
> Url: https://github.com/boostorg/math/pull/819#discussion_r951777988  

If bit_cast is unavailable, would it be better to static_assert if the argument is zero or NaN on the grounds that we just don't know what the result should be?

> Username: mborland  
> Created_at: 2022-08-23 01:43:17 UTC  
> Url: https://github.com/boostorg/math/pull/819#discussion_r952062912  

I think that is fair, and also matches what we discussed on the ML.


---

## Comment 5

> Username: mborland  
> Created_at: 2022-08-23 14:25:46 UTC  
> Url: https://github.com/boostorg/math/pull/819#issuecomment-1224153022  

@jzmaddock Would you like another look before I merge? I hit all of your comments in the latest commit.

---
