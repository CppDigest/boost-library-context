# #794 Version 118 [Merged]

> Username: vinniefalco  
> Created at: 2017-09-21 23:13:08 UTC  
> Updated at: 2017-10-01 17:09:13 UTC  
> Merged at: 2017-09-29 13:22:46 UTC  
> Closed at: 2017-09-29 13:22:46 UTC  
> Url: https://github.com/boostorg/beast/pull/794  

* file_win32 opens for read-only in shared mode  
* Remove unused strands in server examples  
* Update build instructions  
* Doc root is at index.html  
  
HTTP:  
  
* Fix writing header into std::ostream

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-09-22 16:31:46 UTC  
> Updated_at: 2017-09-26 19:13:19 UTC  
> Url: https://github.com/boostorg/beast/pull/794#issuecomment-331496133  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=h1) Report  
> Merging [#794](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/0f5ea371c18a6797f4623a61f159326428c01400?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/794/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #794      +/-   ##  
===========================================  
+ Coverage     95.7%   95.71%   +<.01%       
===========================================  
  Files          105      105                
  Lines        10534    10547      +13       
===========================================  
+ Hits         10082    10095      +13       
  Misses         452      452  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `93.53% <100%> (+0.08%)` | :arrow_up: |  
| [include/boost/beast/core/ostream.hpp](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL29zdHJlYW0uaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/ostream.hpp](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9vc3RyZWFtLmhwcA==) | `95.74% <0%> (+0.87%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=footer). Last update [0f5ea37...e35ae82](https://codecov.io/gh/boostorg/beast/pull/794?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
