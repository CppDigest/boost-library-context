# #36 Do not create rational from array type. [Merged]

> Username: SpiderCh  
> Created at: 2019-03-16 06:13:25 UTC  
> Updated at: 2019-04-16 15:42:54 UTC  
> Merged at: 2019-04-16 15:42:54 UTC  
> Closed at: 2019-04-16 15:42:54 UTC  
> Url: https://github.com/boostorg/rational/pull/36  

For fixing issue #26

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-03-16 11:22:36 UTC  
> Updated_at: 2019-03-16 11:24:20 UTC  
> Url: https://github.com/boostorg/rational/pull/36#issuecomment-473521785  

# [Codecov](https://codecov.io/gh/boostorg/rational/pull/36?src=pr&el=h1) Report  
> Merging [#36](https://codecov.io/gh/boostorg/rational/pull/36?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/rational/commit/3fc4192167fa289035878cc4f007208ff6316e1e?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/rational/pull/36/graphs/tree.svg?width=650&token=GWRPyIttVH&height=150&src=pr)](https://codecov.io/gh/boostorg/rational/pull/36?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #36   +/-   ##  
========================================  
  Coverage    67.52%   67.52%             
========================================  
  Files            1        1             
  Lines          271      271             
  Branches        90       90             
========================================  
  Hits           183      183             
  Misses           3        3             
  Partials        85       85  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/rational/pull/36?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/rational.hpp](https://codecov.io/gh/boostorg/rational/pull/36/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9yYXRpb25hbC5ocHA=) | `67.52% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/rational/pull/36?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/rational/pull/36?src=pr&el=footer). Last update [3fc4192...834279b](https://codecov.io/gh/boostorg/rational/pull/36?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-04-15 16:50:27 UTC  
> Url: https://github.com/boostorg/rational/pull/36#issuecomment-483331506  

This all looks sensible to me - my only comment is that it's half a fix, in the sense that various other types will also fail to instantiate std::numeric_limits: abstract types, function types and incomplete types at the very least.  Some of those are probably fixable, incomplete types probably not so much.  
  
I'd also like to test the changes against Boost.Multiprecision for any breakages.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-04-15 18:59:57 UTC  
> Url: https://github.com/boostorg/rational/pull/36#issuecomment-483376166  

>I'd also like to test the changes against Boost.Multiprecision for any breakages.  
  
Done, all looks OK.

---
