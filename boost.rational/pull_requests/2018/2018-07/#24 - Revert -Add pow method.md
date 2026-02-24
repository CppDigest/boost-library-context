# #24 Revert "Add pow method" [Merged]

> Username: jeking3  
> Created at: 2018-07-13 20:28:42 UTC  
> Updated at: 2018-07-14 15:19:06 UTC  
> Merged at: 2018-07-14 15:18:53 UTC  
> Closed at: 2018-07-14 15:18:53 UTC  
> Url: https://github.com/boostorg/rational/pull/24  

This reverts commit 25ad8e5a5cc382c483dde8d6c019ba970b443f62.  
  
During beta testing it was found the addition of boost::pow broke some code.  More details can be found here:  
  
https://github.com/boostorg/rational/pull/23  
  
Corresponding release note change:  
  
https://github.com/boostorg/website/pull/350

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-07-13 21:53:11 UTC  
> Updated_at: 2018-07-13 21:56:57 UTC  
> Url: https://github.com/boostorg/rational/pull/24#issuecomment-404963491  

# [Codecov](https://codecov.io/gh/boostorg/rational/pull/24?src=pr&el=h1) Report  
> Merging [#24](https://codecov.io/gh/boostorg/rational/pull/24?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/rational/commit/370e31384762651a5d11d4724b32295953914ece?src=pr&el=desc) will **increase** coverage by `0.26%`.  
> The diff coverage is `50%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/rational/pull/24/graphs/tree.svg?height=150&width=650&token=GWRPyIttVH&src=pr)](https://codecov.io/gh/boostorg/rational/pull/24?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #24      +/-   ##  
===========================================  
+ Coverage    67.25%   67.52%   +0.26%       
===========================================  
  Files            1        1                
  Lines          281      271      -10       
  Branches        97       90       -7       
===========================================  
- Hits           189      183       -6       
  Misses           3        3                
+ Partials        89       85       -4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/rational/pull/24?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/rational.hpp](https://codecov.io/gh/boostorg/rational/pull/24/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9yYXRpb25hbC5ocHA=) | `67.52% <50%> (+0.26%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/rational/pull/24?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/rational/pull/24?src=pr&el=footer). Last update [370e313...3b31630](https://codecov.io/gh/boostorg/rational/pull/24?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
