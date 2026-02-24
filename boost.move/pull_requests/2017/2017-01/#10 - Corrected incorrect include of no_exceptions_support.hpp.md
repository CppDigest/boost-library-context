# #10 Corrected incorrect include of no_exceptions_support.hpp. [Merged]

> Username: adamshapiro0  
> Created at: 2017-01-09 15:26:13 UTC  
> Updated at: 2018-08-08 17:51:04 UTC  
> Merged at: 2018-08-07 22:25:01 UTC  
> Closed at: 2018-08-07 22:25:01 UTC  
> Url: https://github.com/boostorg/move/pull/10  

This file appears to have been moved from the `detail` module to the `core` module on 6/2014 in `core` commit 60c9a35d8 (`detail` commit 099854de). It appears that some package managers' Boost distributions have duplicate copies of the file to work around the error.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-08-01 13:42:36 UTC  
> Url: https://github.com/boostorg/move/pull/10#issuecomment-409578878  

@igaztanaga I resolved conflicts / rebased this to get a clean CI run on it.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-08-01 14:04:50 UTC  
> Url: https://github.com/boostorg/move/pull/10#issuecomment-409586395  

# [Codecov](https://codecov.io/gh/boostorg/move/pull/10?src=pr&el=h1) Report  
> Merging [#10](https://codecov.io/gh/boostorg/move/pull/10?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/move/commit/d503fbe1c8334fa8885e67cb83c96aeaf3938555?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/move/pull/10/graphs/tree.svg?width=650&token=dMUaS9WR0b&height=150&src=pr)](https://codecov.io/gh/boostorg/move/pull/10?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           develop     #10   +/-   ##  
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
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/move/pull/10?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/move/algorithm.hpp](https://codecov.io/gh/boostorg/move/pull/10/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9tb3ZlL2FsZ29yaXRobS5ocHA=) | `50% <ø> (ø)` | :arrow_up: |  
| [include/boost/move/algo/move.hpp](https://codecov.io/gh/boostorg/move/pull/10/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9tb3ZlL2FsZ28vbW92ZS5ocHA=) | `94.11% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/move/pull/10?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/move/pull/10?src=pr&el=footer). Last update [d503fbe...14f29a1](https://codecov.io/gh/boostorg/move/pull/10?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-08-02 10:57:45 UTC  
> Url: https://github.com/boostorg/move/pull/10#issuecomment-409888299  

@igaztanaga this is ready for you to merge.

---

## Comment 4

> Username: igaztanaga  
> Created_at: 2018-08-07 22:25:09 UTC  
> Url: https://github.com/boostorg/move/pull/10#issuecomment-411222814  

Many thanks!

---

## Comment 5

> Username: adamshapiro0  
> Created_at: 2018-08-08 17:51:04 UTC  
> Url: https://github.com/boostorg/move/pull/10#issuecomment-411494253  

Thanks for pushing this along folks, and glad I could help!

---
