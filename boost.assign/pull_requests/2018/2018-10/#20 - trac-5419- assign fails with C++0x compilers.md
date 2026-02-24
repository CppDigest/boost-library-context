# #20 trac-5419: assign fails with C++0x compilers [Closed]

> Username: jeking3  
> Created at: 2018-10-08 02:26:06 UTC  
> Updated at: 2018-10-13 14:32:19 UTC  
> Closed at: 2018-10-13 14:32:19 UTC  
> Url: https://github.com/boostorg/assign/pull/20  

This also addresses trac-7364.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-10-08 06:23:08 UTC  
> Url: https://github.com/boostorg/assign/pull/20#issuecomment-427731529  

# [Codecov](https://codecov.io/gh/boostorg/assign/pull/20?src=pr&el=h1) Report  
> Merging [#20](https://codecov.io/gh/boostorg/assign/pull/20?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/assign/commit/56719838495505e9a36cbe49c3119c9550f5ca26?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/assign/pull/20/graphs/tree.svg?width=650&token=SOGsyPqaS7&height=150&src=pr)](https://codecov.io/gh/boostorg/assign/pull/20?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #20   +/-   ##  
========================================  
  Coverage    76.53%   76.53%             
========================================  
  Files           13       13             
  Lines          260      260             
  Branches        55       55             
========================================  
  Hits           199      199             
  Misses          10       10             
  Partials        51       51  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/assign/pull/20?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/assign/list\_of.hpp](https://codecov.io/gh/boostorg/assign/pull/20/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vbGlzdF9vZi5ocHA=) | `79.46% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/assign/pull/20?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/assign/pull/20?src=pr&el=footer). Last update [5671983...fb55c9e](https://codecov.io/gh/boostorg/assign/pull/20?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: morinmorin  
> Created_at: 2018-10-09 12:48:03 UTC  
> Url: https://github.com/boostorg/assign/pull/20#issuecomment-428178337  

That old patch uses `BOOST_NO_FUNCTION_TEMPLATE_DEFAULT_ARGS` (which gets deprecated), but we should use `BOOST_NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS` now.

---

## Comment 3

> Username: morinmorin  
> Created_at: 2018-10-13 14:23:50 UTC  
> Url: https://github.com/boostorg/assign/pull/20#issuecomment-429546242  

I noticed that PR #6 includes a fix for Trac tickets 5419 and 7364. So let's abandon this PR.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-10-13 14:32:19 UTC  
> Url: https://github.com/boostorg/assign/pull/20#issuecomment-429546861  

Sounds good.

---
