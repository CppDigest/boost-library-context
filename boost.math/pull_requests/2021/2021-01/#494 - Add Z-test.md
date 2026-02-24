# #494 Add Z-test [Merged]

> Username: mborland  
> Created at: 2021-01-16 15:10:00 UTC  
> Updated at: 2021-01-17 18:29:36 UTC  
> Merged at: 2021-01-17 14:53:49 UTC  
> Closed at: 2021-01-17 14:53:50 UTC  
> Url: https://github.com/boostorg/math/pull/494  

Adds one and two sample z-tests. The implementation is nearly identical to t-test (as it should be).

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-16 15:55:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/494#pullrequestreview-569948241  

📁 doc/statistics/z_test.qbk

```diff
  15 |+ namespace boost::math::statistics {
  16 |+ 
  17 |+ template<typename Real, typename Size = Real>
```

> Username: NAThompson  
> Created_at: 2021-01-16 15:55:20 UTC  
> Updated_at: 2021-01-17 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/494#discussion_r558938127  

Is `typename Size = Real` a good naming convention here? I feel like this default may be incorrect.

> Username: mborland  
> Created_at: 2021-01-16 16:10:54 UTC  
> Updated_at: 2021-01-17 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/494#discussion_r558944791  

It is incorrect. I was toying with not having to call `Real(sample_size)` but removed it.


---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-16 15:57:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/494#pullrequestreview-569948394  

📁 test/Jamfile.v2

```diff
 980 |    [ run ljung_box_test.cpp : : : [ requires cxx17_if_constexpr cxx17_std_apply ] ]
 981 |    [ run test_t_test.cpp : : : [ requires cxx17_if_constexpr cxx17_std_apply ] ]
 982 |+    [ run test_z_test.cpp : : : [ requires cxx17_if_constexpr cxx17_std_apply ] ]
```

> Username: NAThompson  
> Created_at: 2021-01-16 15:57:43 UTC  
> Updated_at: 2021-01-17 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/494#discussion_r558939047  

Do you need the `requires cxx17_*` here? It looks like you've avoided type deduction using C++17 `if constexpr`.

> Username: mborland  
> Created_at: 2021-01-16 16:13:47 UTC  
> Updated_at: 2021-01-17 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/494#discussion_r558945929  

There is a dependency on univariate statistics which is currently C++17. Once PR #434 is merged everything I've recently edited in the stats library will drop to C++11.


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-16 15:59:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/494#pullrequestreview-569948506  

📁 test/test_z_test.cpp

```diff
  24 |+ 
  25 |+     auto [computed_statistic_3, computed_pvalue_3] = boost::math::statistics::one_sample_z_test(Real(1.0/2.0), Real(10), Real(100), Real(1.0/3.0));
  26 |+     CHECK_ULP_CLOSE(Real(1.0/6.0), computed_statistic_3, 5);
```

> Username: NAThompson  
> Created_at: 2021-01-16 15:59:14 UTC  
> Updated_at: 2021-01-17 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/494#discussion_r558939833  

This will never be more accurate than double precision. Use `Real(1)/6`.


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-16 16:00:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/494#pullrequestreview-569948571  

📁 test/test_z_test.cpp

```diff
  46 |+     auto [computed_statistic, computed_pvalue] = boost::math::statistics::two_sample_z_test(set_2, set_1);
  47 |+     CHECK_ULP_CLOSE(Real(1), computed_statistic, 5);
  48 |+     CHECK_MOLLIFIED_CLOSE(Real(0), computed_pvalue, 1e-22);
```

> Username: NAThompson  
> Created_at: 2021-01-16 16:00:06 UTC  
> Updated_at: 2021-01-17 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/494#discussion_r558940347  

Where is the `1e-22` coming from here?

> Username: mborland  
> Created_at: 2021-01-16 16:24:07 UTC  
> Updated_at: 2021-01-17 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/494#discussion_r558949862  

Changed to `5*std::numeric_limits<Real>::epsilon()`.


---

## Comment 5

> Username: mborland  
> Created_at: 2021-01-16 16:36:18 UTC  
> Url: https://github.com/boostorg/math/pull/494#issuecomment-761593127  

@NAThompson Once a message get posted on the ML that build is fixed I'll cycle this through CI.

---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-16 17:40:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/494#pullrequestreview-569955151  

📁 test/test_z_test.cpp

```diff
  24 |+     CHECK_ULP_CLOSE(Real(25), computed_statistic_2, 5);
  25 |+ 
  26 |+     auto [computed_statistic_3, computed_pvalue_3] = boost::math::statistics::one_sample_z_test(Real(1.0/2.0), Real(10), Real(100), Real(1.0/3.0));
```

> Username: NAThompson  
> Created_at: 2021-01-16 17:40:40 UTC  
> Updated_at: 2021-01-17 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/494#discussion_r558978273  

Same problem here: 1.0/3.0 is only double precision accurate.


---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-16 22:48:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/494#pullrequestreview-569973000  

📁 doc/statistics/z_test.qbk

```diff
  13 |+ #include <boost/math/statistics/z_test.hpp>
  14 |+ 
  15 |+ namespace boost::math::statistics {
```

> Username: NAThompson  
> Created_at: 2021-01-16 22:48:50 UTC  
> Updated_at: 2021-01-17 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/494#discussion_r559049129  

Says below that these are C++11 compatible, so maybe remove the C++17 syntax.


---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-01-16 22:50:08 UTC  
> Url: https://github.com/boostorg/math/pull/494#issuecomment-761692539  

For some reason the LaTex came out looking horrible for me:  
  
<img width="845" alt="Screen Shot 2021-01-16 at 5 49 31 PM" src="https://user-images.githubusercontent.com/5459618/104824803-374fac00-5823-11eb-8817-0faecaec5596.png">  
  
Does it look like this for you?

---

## Comment 9

> Username: mborland  
> Created_at: 2021-01-17 05:35:05 UTC  
> Url: https://github.com/boostorg/math/pull/494#issuecomment-761737796  

It has been replaced by a Latex generated SVG. That does not look great.

---

## Comment 10

> Username: mborland  
> Created_at: 2021-01-17 13:13:46 UTC  
> Url: https://github.com/boostorg/math/pull/494#issuecomment-761810867  

@NAThompson The only CI failure has been fixed in commit 66f3a5f. Should be good to go.

---

## Comment 11

> Username: NAThompson  
> Created_at: 2021-01-17 14:55:02 UTC  
> Url: https://github.com/boostorg/math/pull/494#issuecomment-761825145  

@mborland : Docs look good to me now. For reference, I use this tool to create my equations: https://viereck.ch/latex-to-svg/  
  
Probably many similar tools are out there . . .

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2021-01-17 16:47:10 UTC  
> Url: https://github.com/boostorg/math/pull/494#issuecomment-761842159  

>Probably many similar tools are out there . . .  
  
This site works well IMO: http://www.tlhiv.org/ltxpreview/

---
