# #672 Constexpr abs/fabs [Merged]

> Username: mborland  
> Created at: 2021-08-11 18:35:56 UTC  
> Updated at: 2021-08-17 18:44:54 UTC  
> Merged at: 2021-08-17 05:08:12 UTC  
> Closed at: 2021-08-17 05:08:12 UTC  
> Url: https://github.com/boostorg/math/pull/672  



---

## Comment 1

> Username: mborland  
> Created_at: 2021-08-13 14:32:08 UTC  
> Url: https://github.com/boostorg/math/pull/672#issuecomment-898499622  

@jzmaddock and @NAThompson I think this is ready for review. The drone build failures are all network timeout issues.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2021-08-13 15:07:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/672#pullrequestreview-729715712  

📁 doc/sf/ccmath.qbk

```diff
  12 |- Constexpr implementations of the functionality found in `<cmath>`. The following functions require C++17 or greater.
  12 |+ Constexpr implementations of the functionality found in `<cmath>`.
  13 |+ In a constexpr context the functions will use an implementation defined in boost.
```

> Username: NAThompson  
> Created_at: 2021-08-13 15:07:21 UTC  
> Updated_at: 2021-08-13 15:07:22 UTC  
> Url: https://github.com/boostorg/math/pull/672#discussion_r688585922  

Could you make sure `constexpr` is formatted as code?


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2021-08-13 15:10:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/672#pullrequestreview-729718971  

📁 test/ccmath_abs_test.cpp

```diff
  21 |+     static_assert(boost::math::ccmath::abs(T(-3)) == 3);
  22 |+     static_assert(boost::math::ccmath::abs(T(-0)) == 0);
  23 |+     static_assert(boost::math::ccmath::abs(-std::numeric_limits<T>::infinity()) == std::numeric_limits<T>::infinity());
```

> Username: NAThompson  
> Created_at: 2021-08-13 15:10:32 UTC  
> Updated_at: 2021-08-13 15:10:33 UTC  
> Url: https://github.com/boostorg/math/pull/672#discussion_r688588325  

Could some very simple expressions be joined here? eg  
  
```  
static_assert(abs(sin(1)) == ...)  
```

> Username: mborland  
> Created_at: 2021-08-13 16:16:17 UTC  
> Url: https://github.com/boostorg/math/pull/672#discussion_r688630910  

The difficulty is the only functions guaranteed to be `constexpr` are `pow` and `sqrt`. I could do something heavily restricted like only testing your suggestion on GCC10+ where some of `cmath` is `constexpr`. Thoughts?

> Username: NAThompson  
> Created_at: 2021-08-13 16:26:28 UTC  
> Updated_at: 2021-08-13 16:26:29 UTC  
> Url: https://github.com/boostorg/math/pull/672#discussion_r688637067  

Yeah, I think if you have a guarantee like that, then you should *try* to test it. If it dies in CI, well, not a big deal.


---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-08-13 16:26:52 UTC  
> Updated_at: 2021-08-13 16:27:03 UTC  
> Url: https://github.com/boostorg/math/pull/672#issuecomment-898580343  

Unrelated point: I feel like after this is merged we should consider doing another standalone release.

---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2021-08-13 21:02:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/672#pullrequestreview-729968832  

📁 test/ccmath_abs_test.cpp

```diff
  39 |+     constexpr T sin_1 = boost::math::ccmath::abs(std::sin(T(-1)));
  40 |+     static_assert(sin_1 > 0);
  41 |+     static_assert(boost::math::ccmath::abs(sin_1 - 0.8414709848078965066525l) < tol);
```

> Username: NAThompson  
> Created_at: 2021-08-13 21:02:58 UTC  
> Updated_at: 2021-08-13 21:02:59 UTC  
> Url: https://github.com/boostorg/math/pull/672#discussion_r688779514  

I believe this should be bitwise equal to the correctly rounded result, so `abs(sin_1 - expected) < tol` is too loose. Maybe use a hexadecimal floating point literal and check for bitwise equality?

> Username: NAThompson  
> Created_at: 2021-08-13 21:03:10 UTC  
> Url: https://github.com/boostorg/math/pull/672#discussion_r688779582  

Nice test, btw.


---

## Comment 6

> Username: mborland  
> Created_at: 2021-08-14 13:42:53 UTC  
> Updated_at: 2021-08-14 13:43:25 UTC  
> Url: https://github.com/boostorg/math/pull/672#issuecomment-898896203  

@NAThompson This is clean with the changed test. All of the drone build failures are timeouts.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2021-08-14 15:30:58 UTC  
> Url: https://github.com/boostorg/math/pull/672#issuecomment-898908418  

I'm happy with these changes, but this is something @jzmaddock understands better than me.  
  
Also make sure to squash merge since there were a bunch of "fight the CI commits" which will make `git bisect` painful.

---
