# #41 Fix arbitrary precision rationals with negative denominator [Merged]

> Username: psavouli  
> Created at: 2020-02-12 21:25:11 UTC  
> Updated at: 2025-07-03 16:43:06 UTC  
> Merged at: 2025-07-03 16:43:06 UTC  
> Closed at: 2025-07-03 16:43:06 UTC  
> Url: https://github.com/boostorg/rational/pull/41  

This fixes https://github.com/boostorg/rational/issues/27.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-13 01:36:41 UTC  
> Url: https://github.com/boostorg/rational/pull/41#issuecomment-585505850  

# [Codecov](https://codecov.io/gh/boostorg/rational/pull/41?src=pr&el=h1) Report  
> Merging [#41](https://codecov.io/gh/boostorg/rational/pull/41?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/rational/commit/47c1b26964b06a99200769474d58a309267c50f1?src=pr&el=desc) will **decrease** coverage by `3.19%`.  
> The diff coverage is `0%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/rational/pull/41/graphs/tree.svg?width=650&token=GWRPyIttVH&height=150&src=pr)](https://codecov.io/gh/boostorg/rational/pull/41?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop      #41     +/-   ##  
==========================================  
- Coverage    70.11%   66.91%   -3.2%       
==========================================  
  Files            1        1               
  Lines          271      272      +1       
  Branches        90       94      +4       
==========================================  
- Hits           190      182      -8       
  Misses           3        3               
- Partials        78       87      +9  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/rational/pull/41?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/rational.hpp](https://codecov.io/gh/boostorg/rational/pull/41/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9yYXRpb25hbC5ocHA=) | `66.91% <0%> (-3.2%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/rational/pull/41?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/rational/pull/41?src=pr&el=footer). Last update [47c1b26...ae740e4](https://codecov.io/gh/boostorg/rational/pull/41?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: psavouli  
> Created_at: 2020-02-20 22:05:00 UTC  
> Url: https://github.com/boostorg/rational/pull/41#issuecomment-589362602  

Could I have some help here please? I do not quite know how the coverage report is setup to reproduce it. Equally the master and develop branches show 70% and 67% coverage respectively, even though both branches are the [same](https://github.com/boostorg/rational/compare/develop...master). So I can't quite tell if there is actually something wrong with my change or something else is going on. Thanks.

---

## Comment 3

> Username: bjodah  
> Created_at: 2020-05-18 09:56:58 UTC  
> Url: https://github.com/boostorg/rational/pull/41#issuecomment-630077504  

Just wanted to report that this patch works wrt. this issue in downstream SymEngine:  
https://github.com/symengine/symengine/issues/1639

---

## Comment 4

> Username: jeking3  
> Created_at: 2025-07-03 14:16:47 UTC  
> Url: https://github.com/boostorg/rational/pull/41#issuecomment-3032447805  

I had to fix the test jamfile to depend on multiprecision for rational_test.cpp, and rebased on develop.

---

## Comment 5

> Username: jeking3  
> Created_at: 2025-07-03 14:26:18 UTC  
> Url: https://github.com/boostorg/rational/pull/41#issuecomment-3032478555  

Coverage report text here is stale (but the heatmap is accurate: 100%), clicking through shows the new code paths are tested.

---

## Comment 6

> Username: jeking3  
> Created_at: 2025-07-03 15:06:55 UTC  
> Url: https://github.com/boostorg/rational/pull/41#issuecomment-3032615397  

Didn't think it would be this messy!

---
