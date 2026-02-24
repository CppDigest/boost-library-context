# #931 Remove first_ and last_ from win32 file_body [Closed]

> Username: djarek  
> Created at: 2017-12-09 21:53:31 UTC  
> Updated at: 2017-12-09 22:12:23 UTC  
> Closed at: 2017-12-09 21:57:29 UTC  
> Url: https://github.com/boostorg/beast/pull/931  

These members were never assigned values other than `0` and `size_` respectively.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-12-09 21:55:38 UTC  
> Url: https://github.com/boostorg/beast/pull/931#issuecomment-350508294  

You have it exactly backwards. Rather than removing `first_` and `last_`, we should be implementing the **range** feature, where `file_body` can be used to deliver a subset of the file.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-12-09 21:56:19 UTC  
> Url: https://github.com/boostorg/beast/pull/931#issuecomment-350508332  

See https://github.com/boostorg/beast/issues/723

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2017-12-09 22:08:36 UTC  
> Url: https://github.com/boostorg/beast/pull/931#issuecomment-350509061  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/931?src=pr&el=h1) Report  
> Merging [#931](https://codecov.io/gh/boostorg/beast/pull/931?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/8414dbf73b50d4b67bd933f2fe20863dd73c3de5?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/931/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/931?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #931   +/-   ##  
========================================  
  Coverage    95.71%   95.71%             
========================================  
  Files          104      104             
  Lines        10453    10453             
========================================  
  Hits         10005    10005             
  Misses         448      448  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/931?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/931?src=pr&el=footer). Last update [8414dbf...2d52a78](https://codecov.io/gh/boostorg/beast/pull/931?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
