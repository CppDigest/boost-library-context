# #388 boost::math::pow is not constexpr #354 [Merged]

> Username: mborland  
> Created at: 2020-07-03 02:51:54 UTC  
> Updated at: 2021-01-26 19:25:19 UTC  
> Merged at: 2020-07-05 17:33:38 UTC  
> Closed at: 2020-07-05 17:33:38 UTC  
> Url: https://github.com/boostorg/math/pull/388  

Made pow constexpr as requested in issue #354. Forward declaring constexpr functions requires the language standard to be at least C++14. The if statements were left as is due to making them constexpr as well would raise the language standard to 17. The only change that would have is determining which of the return statements would compile. If you compile with -std=c++11 you will receive 6 [-Wc++14-extension] warnings but the results will remain as expected. Please let me know if you have any questions.

---

## Review 1 [Commented]

> Username: pabristow  
> Created_at: 2020-07-03 09:27:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/388#pullrequestreview-442279273  

It would be great if this works - but it must work for the Boost.Math tests & examples on the Big Three platforms.  Are you able to test this, or are you relying the CI to do this?  
  
(My experience with constexpr has been discouraging.  For example, it recently became clear that even ceil wasn't constexpr on all platforms :-(   Sigh.)

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-07-03 14:38:31 UTC  
> Updated_at: 2020-07-03 14:40:29 UTC  
> Url: https://github.com/boostorg/math/pull/388#issuecomment-653577985  

@mborland : Starting to look good. Could you throw a `static_assert(boost::math::pow<8>(2)==256, "Pow is not constexpr");` into `test/pow_test.cpp`? Then we'll be able to work out how many platforms we'll be able to support.  
  
I think @jzmaddock has a `BOOST_CONSTEXPR` macro that might help with portability, as well as a `BOOST_IF_CONSTEXPR`.

---

## Comment 3

> Username: mborland  
> Created_at: 2020-07-03 15:30:12 UTC  
> Url: https://github.com/boostorg/math/pull/388#issuecomment-653597594  

@pabristow I did some testing locally to make sure that it worked on my machine first. I was going to rely on CI to check all platforms.  
  
@NAThompson Added as requested.

---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-03 18:40:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/388#pullrequestreview-442533247  

📁 include/boost/math/special_functions/pow.hpp

```diff
  48 |     template <typename T>
  49 |-     static T result(T base)
  49 |+     static constexpr T result(T base)
```

> Username: jzmaddock  
> Created_at: 2020-07-03 18:40:57 UTC  
> Updated_at: 2020-07-03 19:47:02 UTC  
> Url: https://github.com/boostorg/math/pull/388#discussion_r449680237  

These require C++14 constexpr anyway?  
  
So I would change `constexpr` to `BOOST_CXX14_CONSTEXPR` and make the constexpr test conditional on BOOST_NO_CXX14_CONSTEXPR not being defined.  While it might be possible to rewrite these for C++11 constexpr it risks wrecking runtime performance.

> Username: mborland  
> Created_at: 2020-07-03 19:21:52 UTC  
> Updated_at: 2020-07-03 19:47:02 UTC  
> Url: https://github.com/boostorg/math/pull/388#discussion_r449687439  

@jzmaddock Most functions in this header requires C++14 either due to variable declaration in the function body, or multiple return statements. I will proceed with the recommendation to change to BOOST_CXX_14_CONSTEXPR rather than bringing everything down to C++11 standard using obfuscated code.


---

## Comment 5

> Username: mborland  
> Created_at: 2020-07-05 17:00:45 UTC  
> Url: https://github.com/boostorg/math/pull/388#issuecomment-653912665  

@NAThompson It looks like the two tests that ran and failed are the same two that failed in your PR #387. Is this the expected result?

---

## Comment 6

> Username: NAThompson  
> Created_at: 2020-07-05 17:31:21 UTC  
> Url: https://github.com/boostorg/math/pull/388#issuecomment-653916135  

@mborland : Yup, those are the known serialization issues.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-07-05 17:34:05 UTC  
> Url: https://github.com/boostorg/math/pull/388#issuecomment-653916465  

@mborland : Thanks for the diffs; hope you had fun and learned a bit!

---
