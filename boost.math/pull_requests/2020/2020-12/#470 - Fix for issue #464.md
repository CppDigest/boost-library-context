# #470 Fix for issue #464 [Merged]

> Username: mborland  
> Created at: 2020-12-24 07:00:09 UTC  
> Updated at: 2021-01-09 19:21:46 UTC  
> Merged at: 2020-12-30 19:01:55 UTC  
> Closed at: 2020-12-30 19:01:55 UTC  
> Url: https://github.com/boostorg/math/pull/470  

Fix for issue #464. `std::real` and `std::imag` did not become constexpr until c++14. Change function signature from `BOOST_CONSTEXPR` to `BOOST_CXX14_CONSTEXPR`.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-12-26 18:34:18 UTC  
> Url: https://github.com/boostorg/math/pull/470#issuecomment-751383862  

Looks good, thanks for that!

---

## Comment 2

> Username: mborland  
> Created_at: 2020-12-28 12:34:58 UTC  
> Url: https://github.com/boostorg/math/pull/470#issuecomment-751698324  

@jzmaddock This is good to go; CI is clean.

---
