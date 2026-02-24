# #1210 fix http_server_stackless_ssl.cpp example [Closed]

> Username: felixguendling  
> Created at: 2018-07-26 12:57:21 UTC  
> Updated at: 2018-07-28 14:55:48 UTC  
> Closed at: 2018-07-28 14:55:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1210  

write to self_.stream_ instead of the socket_ directly

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-07-26 13:21:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1210#issuecomment-408095371  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1210?src=pr&el=h1) Report  
> Merging [#1210](https://codecov.io/gh/boostorg/beast/pull/1210?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/e23ecc8ac903b303b9d1a9824b97c092cb3c09bd?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1210/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1210?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1210   +/-   ##  
========================================  
  Coverage    95.52%   95.52%             
========================================  
  Files          113      113             
  Lines        11062    11062             
========================================  
  Hits         10567    10567             
  Misses         495      495  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1210?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1210?src=pr&el=footer). Last update [e23ecc8...e2babe5](https://codecov.io/gh/boostorg/beast/pull/1210?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-07-26 13:43:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1210#issuecomment-408102113  

You're right!

---
