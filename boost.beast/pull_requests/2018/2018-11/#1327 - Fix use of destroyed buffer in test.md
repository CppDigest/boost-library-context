# #1327 Fix use of destroyed buffer in test [Closed]

> Username: djarek  
> Created at: 2018-11-27 19:10:36 UTC  
> Updated at: 2019-09-10 21:03:43 UTC  
> Closed at: 2018-12-01 15:02:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1327  

b was accessed by a move constructor of the unexecuted websocket read operation.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-27 19:30:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1327#issuecomment-442186608  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1327?src=pr&el=h1) Report  
> Merging [#1327](https://codecov.io/gh/boostorg/beast/pull/1327?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/770308cdcc9b758f7e282e886c73d7efde529e57?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1327/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1327?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1327   +/-   ##  
========================================  
  Coverage    92.05%   92.05%             
========================================  
  Files          124      124             
  Lines        11505    11505             
========================================  
  Hits         10591    10591             
  Misses         914      914  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1327?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1327?src=pr&el=footer). Last update [770308c...5392ead](https://codecov.io/gh/boostorg/beast/pull/1327?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-11-27 19:36:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1327#issuecomment-442188569  

Needs a colon on the first line (since there is continuation text). I will amend the commit message when I merge it.

---
