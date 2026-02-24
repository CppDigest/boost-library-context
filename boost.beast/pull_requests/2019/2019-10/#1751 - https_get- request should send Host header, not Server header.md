# #1751 https_get: request should send Host header, not Server header [Closed]

> Username: RockinRoel  
> Created at: 2019-10-31 17:23:23 UTC  
> Updated at: 2019-11-06 12:54:04 UTC  
> Closed at: 2019-11-06 12:54:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1751  

If you try the https_get example with the Server header, servers will  
respond with 400 Bad Request. This example works as it should when it  
uses the Host header.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-10-31 17:41:41 UTC  
> Url: https://github.com/boostorg/beast/pull/1751#issuecomment-548489937  

Can't argue with this, lol - thanks!

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-10-31 18:44:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1751#issuecomment-548516419  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1751?src=pr&el=h1) Report  
> Merging [#1751](https://codecov.io/gh/boostorg/beast/pull/1751?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/bb85859e96d2815c0a49701733ce4d15f7b156d0?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1751/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1751?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1751   +/-   ##  
========================================  
  Coverage    95.13%   95.13%             
========================================  
  Files          156      156             
  Lines        11943    11943             
========================================  
  Hits         11362    11362             
  Misses         581      581  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1751?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1751?src=pr&el=footer). Last update [bb85859...fae4653](https://codecov.io/gh/boostorg/beast/pull/1751?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
