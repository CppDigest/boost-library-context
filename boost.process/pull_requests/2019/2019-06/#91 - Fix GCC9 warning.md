# #91 Fix GCC9 warning [Merged]

> Username: orgads  
> Created at: 2019-06-26 10:12:02 UTC  
> Updated at: 2020-08-04 19:25:02 UTC  
> Merged at: 2019-06-26 10:30:19 UTC  
> Closed at: 2019-06-26 10:30:19 UTC  
> Url: https://github.com/boostorg/process/pull/91  

warning: moving a local object in a return statement prevents copy elision

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-06-26 10:32:38 UTC  
> Url: https://github.com/boostorg/process/pull/91#issuecomment-505818822  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/91?src=pr&el=h1) Report  
> Merging [#91](https://codecov.io/gh/boostorg/process/pull/91?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/cf7ad3643818c84b308cdedb533b1c4553dd246c?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/91/graphs/tree.svg?width=650&token=AhunMqTSpA&height=150&src=pr)](https://codecov.io/gh/boostorg/process/pull/91?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #91   +/-   ##  
========================================  
  Coverage    80.73%   80.73%             
========================================  
  Files          110      110             
  Lines         2621     2621             
========================================  
  Hits          2116     2116             
  Misses         505      505  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/91?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/environment.hpp](https://codecov.io/gh/boostorg/process/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vudmlyb25tZW50LmhwcA==) | `0% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/91?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/91?src=pr&el=footer). Last update [cf7ad36...984c0c5](https://codecov.io/gh/boostorg/process/pull/91?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
