# #1336 Set SO_REUSEADDR in echo_op example [Closed]

> Username: octobanana  
> Created at: 2018-12-03 01:48:35 UTC  
> Updated at: 2018-12-03 22:37:33 UTC  
> Closed at: 2018-12-03 04:24:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1336  

fix #1335

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-12-03 01:54:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1336#issuecomment-443565750  

This is in https://github.com/boostorg/beast/pull/1334 now, waiting on CI

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-12-03 02:30:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1336#issuecomment-443570352  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1336?src=pr&el=h1) Report  
> Merging [#1336](https://codecov.io/gh/boostorg/beast/pull/1336?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/a5739c3ea0526ecf960a19491cac3caa74667137?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1336/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1336?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1336   +/-   ##  
========================================  
  Coverage    92.05%   92.05%             
========================================  
  Files          124      124             
  Lines        11536    11536             
========================================  
  Hits         10620    10620             
  Misses         916      916  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1336?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1336?src=pr&el=footer). Last update [a5739c3...fa714b3](https://codecov.io/gh/boostorg/beast/pull/1336?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2018-12-03 02:49:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1336#issuecomment-443572883  

Hmm.....if we are going to make a bunch of changes to echo-op I think it would be best to put it in a single commit, and we can give it a couple of days to settle would that be OK?

---

## Comment 4

> Username: octobanana  
> Created_at: 2018-12-03 02:59:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1336#issuecomment-443574388  

Sure, I can close the echo-op related pull requests and submit a single one with all the changes.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-12-03 03:00:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1336#issuecomment-443574555  

I think that would be best

---
