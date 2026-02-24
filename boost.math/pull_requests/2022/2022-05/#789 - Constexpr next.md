# #789 Constexpr next [Merged]

> Username: mborland  
> Created at: 2022-05-27 21:12:35 UTC  
> Updated at: 2022-06-29 18:08:43 UTC  
> Merged at: 2022-06-29 15:44:54 UTC  
> Closed at: 2022-06-29 15:44:54 UTC  
> Url: https://github.com/boostorg/math/pull/789  

Implements constexpr: `nextafter`, `nextafterf`, `nextafterl`, `nexttoward`, `nexttowardf`, and `nexttowardl`. Once this and #734 are complete [all functions pending standardization ](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2021/p0533r9.pdf)will be constexpr.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-06-29 03:20:01 UTC  
> Url: https://github.com/boostorg/math/pull/789#issuecomment-1169490586  

@NAThompson Can you review this? It is almost a 1:1 copy of the existing nextafter implementation with standard library cmath functions being replaced by our ccmath functions. The results match exactly which makes me feel good about the other functions.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2022-06-29 14:39:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/789#pullrequestreview-1023498549  

📁 test/ccmath_next_test.cpp

```diff
  47 |+ {
  48 |+     test_next<float>();
  49 |+     test_next<double>();
```

> Username: NAThompson  
> Created_at: 2022-06-29 14:39:24 UTC  
> Url: https://github.com/boostorg/math/pull/789#discussion_r909843564  

@mborland : Is there a way to test for float16?

> Username: mborland  
> Created_at: 2022-06-29 15:37:20 UTC  
> Url: https://github.com/boostorg/math/pull/789#discussion_r910123299  

I don't see any macros in cstdfloat that allow a portable way of checking that `float16_t` is available. I believe that fixed width floating points are being added in C++23 with the reference implementation coming from Boost.Math.


---

## Comment 3

> Username: NAThompson  
> Created_at: 2022-06-29 14:40:44 UTC  
> Url: https://github.com/boostorg/math/pull/789#issuecomment-1170070123  

@mborland : Looks good; make sure to squash merge with a nice commit message.

---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2022-06-29 18:06:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/789#pullrequestreview-1023792841  

📁 include/boost/math/ccmath/next.hpp

```diff
  43 |+ 
  44 |+ template <typename T>
  45 |+ struct has_hidden_guard_digits;
```

> Username: jzmaddock  
> Created_at: 2022-06-29 18:06:46 UTC  
> Url: https://github.com/boostorg/math/pull/789#discussion_r910260472  

In the runtime version of this code, these traits classes were introduced as a point of specialization for multiprecision types that have guard digits (cpp_dec_float, gmp_float).  I think it's probably irrelevant for this use case were we'll be dealing with builtin types only.


---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2022-06-29 18:08:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/789#pullrequestreview-1023794519  

📁 include/boost/math/ccmath/next.hpp

```diff
 197 |+ //
 198 |+ template <typename T>
 199 |+ constexpr T float_next_imp(const T& val, const std::false_type&)
```

> Username: jzmaddock  
> Created_at: 2022-06-29 18:08:14 UTC  
> Url: https://github.com/boostorg/math/pull/789#discussion_r910261647  

Do we actually need to support bases other than 2 in the constexpr case?  I would be inclined to remove this and static_assert that the radix is 2.


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2022-06-29 18:08:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/789#pullrequestreview-1023795100  

📁 include/boost/math/ccmath/next.hpp

```diff
 316 |+ //
 317 |+ template <typename T>
 318 |+ constexpr T float_prior_imp(const T& val, const std::false_type&)
```

> Username: jzmaddock  
> Created_at: 2022-06-29 18:08:43 UTC  
> Url: https://github.com/boostorg/math/pull/789#discussion_r910262047  

as above, possibly delete all this?


---
