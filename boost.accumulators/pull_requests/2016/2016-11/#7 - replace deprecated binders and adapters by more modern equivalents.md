# #7 replace deprecated binders and adapters by more modern equivalents [Merged]

> Username: DanielaE  
> Created at: 2016-11-06 15:37:00 UTC  
> Updated at: 2018-01-01 12:43:08 UTC  
> Merged at: 2017-05-05 16:48:48 UTC  
> Closed at: 2017-05-05 16:48:48 UTC  
> Url: https://github.com/boostorg/accumulators/pull/7  

**replace deprecated binders and adapters by more modern equivalents**  
  
Required with latest msvc versions and compiler option /std:c++latest

---

## Review 1 [Changes requested]

> Username: ericniebler  
> Created_at: 2016-11-06 19:21:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/accumulators/pull/7#pullrequestreview-7339185  

📁 include/boost/accumulators/statistics/tail_variate_means.hpp

```diff
 120 |                   , this->tail_means_.begin()
 121 |-                   , std::bind2nd(std::divides<float_type>(), factor)
 121 |+                   , std::bind(std::divides<float_type>(), std::placeholders::_1, factor)
```

> Username: ericniebler  
> Created_at: 2016-11-06 19:20:46 UTC  
> Updated_at: 2017-05-04 14:59:36 UTC  
> Url: https://github.com/boostorg/accumulators/pull/7#discussion_r86693875  

Boost.Accumulators is portable to pre-C++11 toolsets  that don't have `std::bind`


📁 include/boost/accumulators/statistics/times2_iterator.hpp

```diff
  25 |     typedef transform_iterator<
  26 |-         std::binder1st<std::multiplies<std::size_t> >
  26 |+         decltype(std::bind(std::multiplies<std::size_t>(), 2, std::placeholders::_1))
```

> Username: ericniebler  
> Created_at: 2016-11-06 19:21:04 UTC  
> Updated_at: 2017-05-04 14:59:36 UTC  
> Url: https://github.com/boostorg/accumulators/pull/7#discussion_r86693880  

... and they don't have `decltype` either.


---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-05-04 15:04:04 UTC  
> Url: https://github.com/boostorg/accumulators/pull/7#issuecomment-299212666  

With the advent of the new C++98-deprecation configuration macros I've reworked my patch for maximum compatibility.  
  
Compiles and passes accumulator's testsuite just fine on msvc 9.0, 10.0, 12.0, and 14.1

---

## Comment 3

> Username: ericniebler  
> Created_at: 2017-05-05 16:48:44 UTC  
> Url: https://github.com/boostorg/accumulators/pull/7#issuecomment-299516597  

Much better, thanks!

---
