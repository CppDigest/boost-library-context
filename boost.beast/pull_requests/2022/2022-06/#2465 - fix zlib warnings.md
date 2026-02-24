# #2465 fix zlib warnings [Closed]

> Username: alandefreitas  
> Created at: 2022-06-18 21:39:45 UTC  
> Updated at: 2022-06-22 20:00:25 UTC  
> Closed at: 2022-06-22 20:00:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2465  

fix #2461

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-06-18 22:05:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2465#issuecomment-1159572587  

![pullrequest](https://2465.beast.tracing.cppalliance.org/pullrequest-c457aed0.png)  
Timeline tracing charts: [https://2465.beast.tracing.cppalliance.org/index.html](https://2465.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-06-18 22:31:21 UTC  
> Updated_at: 2022-06-18 22:49:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2465#issuecomment-1159575808  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2465?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2465](https://codecov.io/gh/boostorg/beast/pull/2465?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (373c6ce) into [develop](https://codecov.io/gh/boostorg/beast/commit/c2cff2254a903beb93ea5b434b000dca28f870c5?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c2cff22) will **decrease** coverage by `0.03%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2465/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2465?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2465      +/-   ##  
===========================================  
- Coverage    94.75%   94.71%   -0.04%       
===========================================  
  Files          152      152                
  Lines        11868    11868                
===========================================  
- Hits         11245    11241       -4       
- Misses         623      627       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2465?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2465/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.77% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2465/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.50% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2465?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2465?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c2cff22...373c6ce](https://codecov.io/gh/boostorg/beast/pull/2465?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-06-21 23:29:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2465#issuecomment-1162459510  

If it fixes the warnings then it is good. Were you able to see the warnings before fixing it?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-06-22 20:00:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2465#issuecomment-1163545628  

Merged at 0ed09e59d9d9bb12a97e585ef7472cc85328f8c2

---
