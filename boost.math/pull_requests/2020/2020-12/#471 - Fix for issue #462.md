# #471 Fix for issue #462 [Closed]

> Username: mborland  
> Created at: 2020-12-26 07:05:59 UTC  
> Updated at: 2021-01-11 19:47:30 UTC  
> Closed at: 2021-01-04 18:34:48 UTC  
> Url: https://github.com/boostorg/math/pull/471  

Fix for issue #462. Replace instances of `auto` with `Real`. c1 is over-estimated for all types so compensate ε as constant error coefficient. Clang-11.0.0 passes tests, but issues `-Wimplicit-const-int-float-conversion` warning as noted in issue #430.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2020-12-26 18:28:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/471#pullrequestreview-558886027  

📁 include/boost/math/statistics/linear_regression.hpp

```diff
  64 |- 
  65 |-     auto var_x = boost::math::statistics::variance(x);
  57 |+     BOOST_ASSERT_MSG(std::size(x) > 2, "At least 2 samples are required to perform a linear regression.");
```

> Username: jzmaddock  
> Created_at: 2020-12-26 18:28:49 UTC  
> Updated_at: 2020-12-26 19:40:15 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r549018197  

I'm not sure that asserts are correct here: asserts normally verify our own logic, where these are checking input values.  In particular these checks disappear in release mode which rather worries me here.

> Username: mborland  
> Created_at: 2020-12-26 19:07:49 UTC  
> Updated_at: 2020-12-26 19:40:15 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r549021281  

Makes sense; I'll revert the asserts.  
  
As an aside has anything gone in recently that would have touched [tr1](https://ci.appveyor.com/project/jzmaddock/math/builds/37002310/job/lhb7gba4fatmsyen?fullLog=true#L1302) or [nonfinite_num_facets](https://ci.appveyor.com/project/jzmaddock/math/builds/37002310/job/5e2eaaq6o4gjb8gc?fullLog=true#L3885)? CI is showing a litany of failures with those two (see links). tr1 is ODR linker errors.

> Username: jzmaddock  
> Created_at: 2020-12-26 19:23:33 UTC  
> Updated_at: 2020-12-26 19:40:15 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r549022532  

Nothing has changed at our end - the serialization errors look like they're coming from that lib, the link errors are more worrying as they suggest that lib and exe were built with differing build options.

> Username: jzmaddock  
> Created_at: 2020-12-26 19:55:47 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r549025751  

I can reproduce after updating Boost.Build to latest develop, but have no idea what's going on yet :(

> Username: mborland  
> Created_at: 2020-12-26 20:14:19 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r549027285  

Right before this went to CI Boost.Build had [two](https://github.com/boostorg/build/commit/30a7e738ad2757b811720a56173dd3ba3453eaef) [commits](https://github.com/boostorg/build/commit/8d37b68e1db40ef05ee974e6f263071cd42e6d79) on develop that changed linking on MSVC.


---

## Comment 2

> Username: mborland  
> Created_at: 2020-12-28 12:37:30 UTC  
> Url: https://github.com/boostorg/math/pull/471#issuecomment-751698855  

@jzmaddock Minus all of the strange linker errors from Appveyor the CI results for this looks good.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-12-30 19:01:21 UTC  
> Url: https://github.com/boostorg/math/pull/471#issuecomment-752725157  

I'll let @NAThompson double check before merging this one.

---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-03 16:05:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/471#pullrequestreview-560707901  

📁 include/boost/math/statistics/linear_regression.hpp

```diff
  73 |-     Real c0 = mu_y - c1*mu_x;
  79 |+     // c1 is over-estimated for all types without compensation
  80 |+     const Real c1 = cov_xy/(var_x - std::numeric_limits<Real>::epsilon());
```

> Username: NAThompson  
> Created_at: 2021-01-03 16:05:34 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551023904  

I don't understand this change. Do you have a reference for this?  
  
Could you test it with data that is all < 1e-16? (e.g., the LIGO data is all < 1e-16).

> Username: mborland  
> Created_at: 2021-01-03 19:50:52 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551047093  

I do not have a reference, but empirically it seems to be the case. If you multiply everything by 1e-22 (LIGO-Hanford Range) and represent with `float_quad`, `float_50`, and `float_100` the results are similarly improved.

> Username: NAThompson  
> Created_at: 2021-01-04 14:49:33 UTC  
> Updated_at: 2021-01-04 14:49:34 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551361082  

@mborland : Representing with `float_quad` defeats the purpose. There is no reason to use `float_quad` on LIGO-Handford data; the precision isn't there; double is fine. In this case the denominator becomes negative and the result is wrong.  
  
I still don't understand why this improves the result.

> Username: mborland  
> Created_at: 2021-01-04 15:49:26 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551398247  

50% detection probability is at [3.2e-20](https://image2.slideserve.com/4246672/detection-efficiency-for-narrow-band-bursts-l.jpg) which is why I tested with quadruple and greater precision. The denominator would only go negative if `var_x` is 0 since its one epsilon of Real. The tests for variance only specify <5 epsilon and the result typically has positive error.

> Username: NAThompson  
> Created_at: 2021-01-04 17:53:02 UTC  
> Updated_at: 2021-01-04 17:59:53 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551471537  

3.2e-20 is fine for double precision. Double precision can represent numbers down to 1e-308 without denormals and 1e-324 with.

> Username: NAThompson  
> Created_at: 2021-01-04 17:55:59 UTC  
> Updated_at: 2021-01-04 17:58:30 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551473146  

> The denominator would only go negative if var_x is 0 since its one epsilon of Real.  
  
This is incorrect. You should read up on the rounding model of floating point arithmetic; see Higham, Accuracy and Stability of Numerical Algorithms. (Your statement is correct for *fixed-point* arithmetic.)  
  
A good exercise for you would be to figure out how many representable double precision numbers are < epsilon and >= 0. I think you will be surprised.

> Username: mborland  
> Created_at: 2021-01-04 18:34:43 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551492393  

I'll have to find a copy and give it a read. I can close this as well because it is clearly not correct.


---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-03 16:06:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/471#pullrequestreview-560707968  

📁 include/boost/math/statistics/linear_regression.hpp

```diff
  53 |-     using Real = typename RandomAccessContainer::value_type;
  54 |-     if (x.size() <= 1)
  58 |+     if (std::size(x) <= 1)
```

> Username: NAThompson  
> Created_at: 2021-01-03 16:06:31 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551024020  

I don't have a problem with `std::size`, and in fact it solves a real problem. But it also has historically caused problems which is why I avoid it.  
  
Did you have a type that requires `std::size`?

> Username: mborland  
> Created_at: 2021-01-03 18:58:04 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551042034  

I have no compelling reason other than Scott Myers et. al. recommend it, and I was already changing things.

> Username: NAThompson  
> Created_at: 2021-01-04 14:51:08 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551362025  

Ok, maybe the ecosystem has improved a bit since I first tried it. I'm fine with this change.


---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-03 16:07:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/471#pullrequestreview-560708028  

📁 include/boost/math/statistics/linear_regression.hpp

```diff
  54 |+          typename Size = typename RandomAccessContainer::size_type>
  55 |+ auto simple_ordinary_least_squares_with_R_squared(RandomAccessContainer const & x, RandomAccessContainer const & y)
  56 |+     -> std::tuple<Real, Real, Real>
```

> Username: NAThompson  
> Created_at: 2021-01-03 16:07:25 UTC  
> Updated_at: 2021-01-03 16:08:03 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551024155  

If `Real` is a set of integers, then doesn't this break? And without this change, can't the input data be integral and the output be `double`?

> Username: mborland  
> Created_at: 2021-01-03 20:13:26 UTC  
> Updated_at: 2021-01-03 20:13:27 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551049290  

Like in the event the container passed in is a Matrix? As long is it supports `operator/` and subtraction by a constant than it should behave normally, and if not it would break anyway. `means_and_covariance` returns `std::tuple<Real, Real, Real>` so that should be changed as well if integral types need to return `double`. I know that is the case in almost all of univariate statistics and would be easy enough to change.

> Username: NAThompson  
> Created_at: 2021-01-04 14:51:47 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551362417  

Ok, I do need to check this then. I had a goal of supporting integer data and if I haven't then that needs to be fixed.

> Username: mborland  
> Created_at: 2021-01-04 15:55:44 UTC  
> Updated_at: 2021-01-04 15:55:51 UTC  
> Url: https://github.com/boostorg/math/pull/471#discussion_r551402234  

I can open another PR to add integer support; that would be easy at this point. Replacing the structured binding in `covariance` with a tuple would drop the required language standard from 17 to 11 as well.


---
