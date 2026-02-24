# #41 allow CML to find boost with flag [Closed]

> Username: madmongo1  
> Created at: 2020-02-11 14:59:51 UTC  
> Updated at: 2020-02-14 01:49:06 UTC  
> Closed at: 2020-02-14 01:49:06 UTC  
> Url: https://github.com/boostorg/json/pull/41  

This change allows a standalone Boost.Json to optionally respect the normal `find_package(Boost)` mechanism for finding an installed instance of Boost.  
  
**Existing workflow is not affected in any way.**  
  
setting `-DBOOST_JSON_FIND_BOOST=ON` will require the CML to find Boost, while respecting `BOOST_ROOT` as normal.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-11 16:49:48 UTC  
> Updated_at: 2020-02-11 16:49:51 UTC  
> Url: https://github.com/boostorg/json/pull/41#issuecomment-584732853  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/41?src=pr&el=h1) Report  
> Merging [#41](https://codecov.io/gh/vinniefalco/json/pull/41?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/75528587ff45c9aa0b7d7e0ca379a4f5b36d49fe?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/41/graphs/tree.svg?width=650&token=HNiMmIjyKi&height=150&src=pr)](https://codecov.io/gh/vinniefalco/json/pull/41?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           develop     #41   +/-   ##  
=======================================  
  Coverage     98.2%   98.2%             
=======================================  
  Files           57      57             
  Lines         4856    4856             
=======================================  
  Hits          4769    4769             
  Misses          87      87  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/41?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/41?src=pr&el=footer). Last update [7552858...414e58a](https://codecov.io/gh/vinniefalco/json/pull/41?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-02-14 01:49:05 UTC  
> Url: https://github.com/boostorg/json/pull/41#issuecomment-586058477  

Merged, thanks

---
