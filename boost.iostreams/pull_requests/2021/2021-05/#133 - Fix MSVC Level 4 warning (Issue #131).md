# #133 Fix MSVC Level 4 warning (Issue #131) [Merged]

> Username: mclow  
> Created at: 2021-05-27 14:08:15 UTC  
> Updated at: 2022-11-02 03:04:58 UTC  
> Merged at: 2022-11-02 03:04:57 UTC  
> Closed at: 2022-11-02 03:04:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/133  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-05-27 15:03:32 UTC  
> Updated_at: 2021-05-27 15:06:26 UTC  
> Url: https://github.com/boostorg/iostreams/pull/133#issuecomment-849709943  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/133?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#133](https://codecov.io/gh/boostorg/iostreams/pull/133?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5e60633) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/dd360d941f626ae5844a0b83e39c6bab8ccb32e2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (dd360d9) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/133/graphs/tree.svg?width=650&height=150&src=pr&token=LBEfwtNfca&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/iostreams/pull/133?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #133   +/-   ##  
========================================  
  Coverage    68.84%   68.85%             
========================================  
  Files           80       80             
  Lines         3444     3445    +1       
  Branches      1027     1027             
========================================  
+ Hits          2371     2372    +1       
  Misses         454      454             
  Partials       619      619             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/133?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/iostreams/filter/zlib.hpp](https://codecov.io/gh/boostorg/iostreams/pull/133/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZmlsdGVyL3psaWIuaHBw) | `73.33% <100.00%> (+0.45%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/133?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/133?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [dd360d9...5e60633](https://codecov.io/gh/boostorg/iostreams/pull/133?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: tamminel  
> Created_at: 2022-11-01 20:30:05 UTC  
> Url: https://github.com/boostorg/iostreams/pull/133#issuecomment-1299101209  

@mclow any plans to merge this functionality? This is causing issues with treating warnings as an errors compiler flag

---
