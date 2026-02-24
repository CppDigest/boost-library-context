# #1287 Add missing include in http/read.ipp [Closed]

> Username: djarek  
> Created at: 2018-11-03 15:20:02 UTC  
> Updated at: 2018-11-05 07:00:29 UTC  
> Closed at: 2018-11-04 15:51:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1287  

Fixes a compilation error that occurred when using BOOST_ASIO_SEPARATE_COMPILATION.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-03 19:40:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1287#issuecomment-435615232  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1287?src=pr&el=h1) Report  
> Merging [#1287](https://codecov.io/gh/boostorg/beast/pull/1287?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/8fc9b8579501536e24cc09cdca6a1e0cb10a103d?src=pr&el=desc) will **increase** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1287/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1287?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1287      +/-   ##  
===========================================  
+ Coverage    95.34%   95.36%   +0.01%       
===========================================  
  Files          113      113                
  Lines        11116    11116                
===========================================  
+ Hits         10599    10601       +2       
+ Misses         517      515       -2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1287?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/1287/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `96.33% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/1287/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `100% <0%> (+0.5%)` | :arrow_up: |  
| [include/boost/beast/core/impl/buffers\_suffix.ipp](https://codecov.io/gh/boostorg/beast/pull/1287/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19zdWZmaXguaXBw) | `100% <0%> (+1.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1287?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1287?src=pr&el=footer). Last update [8fc9b85...7156bca](https://codecov.io/gh/boostorg/beast/pull/1287?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-11-04 15:51:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1287#issuecomment-435680875  

Merged

---
