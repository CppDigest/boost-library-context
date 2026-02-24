# #20 Move may_alias attribute for xlC++ compatibility. [Merged]

> Username: chrahunt  
> Created at: 2018-08-31 23:06:53 UTC  
> Updated at: 2018-09-09 03:45:07 UTC  
> Merged at: 2018-09-08 22:17:32 UTC  
> Closed at: 2018-09-08 22:17:33 UTC  
> Url: https://github.com/boostorg/move/pull/20  

This also aligns usage of the `may_alias` attribute with other boost libraries. Fixes #19.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-08-31 23:39:24 UTC  
> Url: https://github.com/boostorg/move/pull/20#issuecomment-417814436  

# [Codecov](https://codecov.io/gh/boostorg/move/pull/20?src=pr&el=h1) Report  
> Merging [#20](https://codecov.io/gh/boostorg/move/pull/20?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/move/commit/61c7a68817da937f97578c8f2a3ce76315cef1f4?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/move/pull/20/graphs/tree.svg?width=650&token=dMUaS9WR0b&height=150&src=pr)](https://codecov.io/gh/boostorg/move/pull/20?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           develop     #20   +/-   ##  
=======================================  
  Coverage     72.5%   72.5%             
=======================================  
  Files           28      28             
  Lines         1695    1695             
  Branches       577     577             
=======================================  
  Hits          1229    1229             
  Misses         109     109             
  Partials       357     357  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/move/pull/20?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/move/core.hpp](https://codecov.io/gh/boostorg/move/pull/20/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9tb3ZlL2NvcmUuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/move/pull/20?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/move/pull/20?src=pr&el=footer). Last update [61c7a68...210da84](https://codecov.io/gh/boostorg/move/pull/20?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2018-09-08 22:17:52 UTC  
> Url: https://github.com/boostorg/move/pull/20#issuecomment-419676437  

Thanks for the patch!

---
