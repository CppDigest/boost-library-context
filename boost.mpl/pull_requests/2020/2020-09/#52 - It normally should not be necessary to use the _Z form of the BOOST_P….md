# #52 It normally should not be necessary to use the _Z form of the BOOST_P… [Merged]

> Username: eldiener  
> Created at: 2020-09-23 12:48:24 UTC  
> Updated at: 2020-09-24 10:07:26 UTC  
> Merged at: 2020-09-24 10:07:26 UTC  
> Closed at: 2020-09-24 10:07:26 UTC  
> Url: https://github.com/boostorg/mpl/pull/52  

…P_ENUM macro, although it is perfectly legal and helpful to do so, but in this case it is necessary as a workaround for a bug in the new VC++ standard conforming preprocessor. The bug manifests itself when testing the TTI library, which internally uses the Boost MPL code. The bug in the new VC++ standard conforming compiler is fixed in the VS2019 preview product, so that fix is sure to find its way to the official VS2019 product sometime soon. In the meantime this "fix" shortens the macro expansion somewhat and, while it should not be necessary, is still helpful.  
  
See https://github.com/boostorg/preprocessor/issues/33 for a report and response to the VC++ new preprocessor bug.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-09-24 05:29:39 UTC  
> Updated_at: 2020-09-24 05:34:10 UTC  
> Url: https://github.com/boostorg/mpl/pull/52#issuecomment-698121283  

# [Codecov](https://codecov.io/gh/boostorg/mpl/pull/52?src=pr&el=h1) Report  
> Merging [#52](https://codecov.io/gh/boostorg/mpl/pull/52?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/mpl/commit/9c0f98f088bbed5943382ebfcfd1b0159aaa7462?el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/mpl/pull/52/graphs/tree.svg?width=650&height=150&src=pr&token=31bBblRchG)](https://codecov.io/gh/boostorg/mpl/pull/52?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #52   +/-   ##  
========================================  
  Coverage    73.33%   73.33%             
========================================  
  Files            5        5             
  Lines           15       15             
  Branches         3        3             
========================================  
  Hits            11       11             
  Misses           1        1             
  Partials         3        3             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/mpl/pull/52?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/mpl/pull/52?src=pr&el=footer). Last update [9c0f98f...4209712](https://codecov.io/gh/boostorg/mpl/pull/52?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
