# #446 Use relaxed increment and acquire_release decrement for atomic reference counts [Closed]

> Username: maximilianriemensberger  
> Created at: 2020-10-08 20:01:01 UTC  
> Updated at: 2020-10-09 18:30:47 UTC  
> Closed at: 2020-10-09 18:30:47 UTC  
> Url: https://github.com/boostorg/json/pull/446  

Implements #446

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-10-08 20:35:34 UTC  
> Updated_at: 2020-10-08 21:39:24 UTC  
> Url: https://github.com/boostorg/json/pull/446#issuecomment-705809018  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/446?src=pr&el=h1) Report  
> Merging [#446](https://codecov.io/gh/boostorg/json/pull/446?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/json/commit/5098730a87148228f65b2dcb40ecec7f15019c09?el=desc) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/446/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/446?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #446   +/-   ##  
========================================  
  Coverage    98.57%   98.57%             
========================================  
  Files           69       69             
  Lines         6044     6044             
========================================  
  Hits          5958     5958             
  Misses          86       86             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/446?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/storage\_ptr.hpp](https://codecov.io/gh/boostorg/json/pull/446/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0b3JhZ2VfcHRyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/446?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/446?src=pr&el=footer). Last update [5098730...32900a3](https://codecov.io/gh/boostorg/json/pull/446?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-10-08 20:39:47 UTC  
> Url: https://github.com/boostorg/json/pull/446#issuecomment-705811022  

It looks like your local git credentials are not set, because the commit is not attached to a GitHub account, would you mind doing `git config` and setting your email and name?

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-10-09 18:30:46 UTC  
> Url: https://github.com/boostorg/json/pull/446#issuecomment-706338604  

merged, thanks!

---
