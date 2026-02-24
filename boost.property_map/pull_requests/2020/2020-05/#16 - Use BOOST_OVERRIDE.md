# #16 Use BOOST_OVERRIDE [Merged]

> Username: EugeneZelenko  
> Created at: 2020-05-17 18:17:41 UTC  
> Updated at: 2022-01-28 11:47:56 UTC  
> Merged at: 2022-01-28 11:47:56 UTC  
> Closed at: 2022-01-28 11:47:56 UTC  
> Url: https://github.com/boostorg/property_map/pull/16  

Use BOOST_OVERRIDE to fix GCC -Wsuggest-override and Clang-tidy modernize-use-override warnings.  
Alphabetical order of STL headers.

---

## Comment 1

> Username: mclow  
> Created_at: 2020-05-18 15:05:23 UTC  
> Url: https://github.com/boostorg/property_map/pull/16#issuecomment-630244353  

Don't you still need the `virtual` keywords?  
Especially for compilers that don't support `override`?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-05-18 15:15:27 UTC  
> Updated_at: 2020-05-18 15:18:51 UTC  
> Url: https://github.com/boostorg/property_map/pull/16#issuecomment-630250848  

# [Codecov](https://codecov.io/gh/boostorg/property_map/pull/16?src=pr&el=h1) Report  
> Merging [#16](https://codecov.io/gh/boostorg/property_map/pull/16?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/property_map/commit/a1510e7e0fb6914c49109ceee1b2c5a83dc84428&el=desc) will **not change** coverage.  
> The diff coverage is `70.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/property_map/pull/16/graphs/tree.svg?width=650&height=150&src=pr&token=IAO17unWyC)](https://codecov.io/gh/boostorg/property_map/pull/16?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #16   +/-   ##  
========================================  
  Coverage    53.07%   53.07%             
========================================  
  Files            5        5             
  Lines          130      130             
  Branches        47       47             
========================================  
  Hits            69       69             
  Misses          17       17             
  Partials        44       44             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/property_map/pull/16?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/property\_map/dynamic\_property\_map.hpp](https://codecov.io/gh/boostorg/property_map/pull/16/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9wZXJ0eV9tYXAvZHluYW1pY19wcm9wZXJ0eV9tYXAuaHBw) | `39.39% <70.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/property_map/pull/16?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/property_map/pull/16?src=pr&el=footer). Last update [a1510e7...65c5133](https://codecov.io/gh/boostorg/property_map/pull/16?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: EugeneZelenko  
> Created_at: 2020-05-18 15:15:57 UTC  
> Updated_at: 2020-05-18 16:06:40 UTC  
> Url: https://github.com/boostorg/property_map/pull/16#issuecomment-630251139  

No, virtual keyword is not required to redefine virtual functions. See https://en.cppreference.com/w/cpp/language/virtual.

---
