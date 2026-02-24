# #55 Improvement to m_do_find_from [Merged]

> Username: joprodrigues  
> Created at: 2020-04-25 17:02:16 UTC  
> Updated at: 2021-01-07 20:10:39 UTC  
> Merged at: 2020-04-26 14:11:37 UTC  
> Closed at: 2020-04-26 14:11:37 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/55  

This new approach decreases the execution time locally. It also expresses better the purpose of the loop.

---

## Review 1 [Changes requested]

> Username: glenfe  
> Created_at: 2020-04-25 20:05:27 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/55#pullrequestreview-400427001  

Can't use a lambda unconditionally as `dynamic_bitset` still needs to support C++03.

---

## Comment 2

> Username: joprodrigues  
> Created_at: 2020-04-25 20:06:35 UTC  
> Updated_at: 2020-04-25 20:07:21 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/55#issuecomment-619433674  

The failing tests use C++03 and C++11, while the same tests in the 64 bits version are not failing and test for C++11 and C++17.  
The tests are failing because I am using a lambda.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-04-26 03:55:01 UTC  
> Updated_at: 2020-04-26 04:03:45 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/55#issuecomment-619477148  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55?src=pr&el=h1) Report  
> Merging [#55](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/3e0107909bb48fa5eb1d7ab09223ad00c9fac9f3&el=desc) will **decrease** coverage by `0.18%`.  
> The diff coverage is `66.66%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55/graphs/tree.svg?width=650&height=150&src=pr&token=PVG5jth1ez)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #55      +/-   ##  
===========================================  
- Coverage    77.20%   77.02%   -0.19%       
===========================================  
  Files            5        4       -1       
  Lines          623      618       -5       
  Branches       217      215       -2       
===========================================  
- Hits           481      476       -5       
  Misses          29       29                
  Partials       113      113                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `75.34% <66.66%> (-0.35%)` | :arrow_down: |  
| [include/boost/dynamic\_bitset/serialization.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9zZXJpYWxpemF0aW9uLmhwcA==) | | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55?src=pr&el=footer). Last update [3e01079...8976e7a](https://codecov.io/gh/boostorg/dynamic_bitset/pull/55?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
