# #968 Fix rounding for non-representable numbers [Merged]

> Username: mborland  
> Created at: 2023-03-17 02:28:44 UTC  
> Updated at: 2023-04-05 15:17:22 UTC  
> Merged at: 2023-04-05 15:17:18 UTC  
> Closed at: 2023-04-05 15:17:18 UTC  
> Url: https://github.com/boostorg/math/pull/968  

Partially addresses re-raised #430. Once CI validates this fix works for `llround` will propagate to the rest of the functions.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-03-17 14:42:22 UTC  
> Url: https://github.com/boostorg/math/pull/968#issuecomment-1473949704  

@jzmaddock any idea why this would break the use of pre-compiled headers under MSVC? See: https://github.com/boostorg/math/actions/runs/4447933117/jobs/7810069256?pr=968#step:11:523

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-03-17 17:51:32 UTC  
> Url: https://github.com/boostorg/math/pull/968#issuecomment-1474201840  

> any idea why this would break the use of pre-compiled headers under MSVC?  
  
No... I wonder if there are any unrelated changes effecting us - build system?  
  
BTW I can't help thinking there may be a simpler solution, let me have a think.

---

## Comment 3

> Username: mborland  
> Created_at: 2023-03-21 21:06:44 UTC  
> Url: https://github.com/boostorg/math/pull/968#issuecomment-1478580028  

The failure is now `9223372036854775807.5` cannot be represented as the target type. I assume this would be dependent on rounding mode but the default will be  `9223372036854775808` which is greater than `LLONG_MAX`.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-03-22 17:56:09 UTC  
> Url: https://github.com/boostorg/math/pull/968#issuecomment-1480019075  

Ah.... the test case is wrong: it assumes that the boundary case on 2^d where there are d digits in the long long is always an integer, as is `float_prior` of that value.  That will hold as long as there are fewer digits in the floating point type than the integer, but not otherwise (long double in this case).  
  
So... I guess either only test floats that have fewer digits than the integer type - since that is the bug case - or else we need to figure out how to fix the test case.  The latter might be tricky, since we're basically duplicating the functionality of llround to carry out the test!  So I'd favour the former for now?

---

## Comment 5

> Username: mborland  
> Created_at: 2023-03-28 14:18:34 UTC  
> Updated_at: 2023-03-28 14:18:45 UTC  
> Url: https://github.com/boostorg/math/pull/968#issuecomment-1486984940  

The PCH issues are likely related to recent changes in B2. I have opened an issue with them:  https://github.com/bfgroup/b2/issues/229

---

## Comment 6

> Username: mborland  
> Created_at: 2023-04-04 06:53:04 UTC  
> Url: https://github.com/boostorg/math/pull/968#issuecomment-1495441515  

@jzmaddock Can you take a look at this now that it's green? The context switching for constexpr calculation is more verbose than I had hoped.

---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2023-04-04 17:39:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/968#pullrequestreview-1371476720  

📁 include/boost/math/special_functions/round.hpp

```diff
  20 |+ #include <cmath>
  21 |+ 
  22 |+ #if __cplusplus >= 201703L || _MSVC_LANG >= 201703L
```

> Username: jzmaddock  
> Created_at: 2023-04-04 17:39:55 UTC  
> Url: https://github.com/boostorg/math/pull/968#discussion_r1157572223  

Minor point, I get nervous about some direct checks like this, should we use BOOST_NO_CXX17_IF_CONSTEXPR instead, or failing that BOOST_CXX_VERSION?

> Username: mborland  
> Created_at: 2023-04-05 12:13:24 UTC  
> Url: https://github.com/boostorg/math/pull/968#discussion_r1158433214  

BOOST_NO_CXX17_IF_CONSTEXPR works because there is a definition for standalone mode already


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2023-04-04 17:41:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/968#pullrequestreview-1371479216  

📁 include/boost/math/special_functions/round.hpp

```diff
  22 |+ #if __cplusplus >= 201703L || _MSVC_LANG >= 201703L
  23 |+ #include <boost/math/ccmath/ldexp.hpp>
  24 |+ #  if !defined(BOOST_MATH_NO_CONSTEXPR_DETECTION) && !defined(BOOST_LIBS_MATH_TEST_INSTANTIATE_HPP) // Concept tests throw off constexpr ldexp
```

> Username: jzmaddock  
> Created_at: 2023-04-04 17:41:20 UTC  
> Updated_at: 2023-04-04 17:41:21 UTC  
> Url: https://github.com/boostorg/math/pull/968#discussion_r1157573750  

The tests pass for me (msvc and cygwin gcc) without the ` && !defined(BOOST_LIBS_MATH_TEST_INSTANTIATE_HPP)` part, and in general the concept checks are there to catch unexpected bugs that are tricky to spot otherwise.  What was failing without this?

> Username: mborland  
> Created_at: 2023-04-05 12:15:27 UTC  
> Updated_at: 2023-04-05 12:15:28 UTC  
> Url: https://github.com/boostorg/math/pull/968#discussion_r1158435325  

This was left over from debugging; I'll remove it.


---

## Comment 9

> Username: jzmaddock  
> Created_at: 2023-04-04 17:42:12 UTC  
> Url: https://github.com/boostorg/math/pull/968#issuecomment-1496358357  

Thanks for this Matt: other than the two minor comments looks good to me!

---
