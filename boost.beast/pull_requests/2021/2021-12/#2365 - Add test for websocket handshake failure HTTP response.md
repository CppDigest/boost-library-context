# #2365 Add test for websocket handshake failure HTTP response [Closed]

> Username: madmongo1  
> Created at: 2021-12-20 18:38:30 UTC  
> Updated at: 2021-12-22 11:04:44 UTC  
> Closed at: 2021-12-22 11:04:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2365  

refs #2364

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-12-21 13:28:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2365#issuecomment-998779720  

![pullrequest](https://2365.beast.tracing.cppalliance.org/pullrequest-0856f9ea.png)  
Timeline tracing charts: [https://2365.beast.tracing.cppalliance.org/index.html](https://2365.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-12-21 13:58:00 UTC  
> Updated_at: 2021-12-21 14:19:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2365#issuecomment-998800778  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2365?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2365](https://codecov.io/gh/boostorg/beast/pull/2365?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (067d0de) into [develop](https://codecov.io/gh/boostorg/beast/commit/55d2ceb627638b4a397367a2b6170aef2b77fc19?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (55d2ceb) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2365/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2365?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2365   +/-   ##  
========================================  
  Coverage    94.85%   94.85%             
========================================  
  Files          151      151             
  Lines        12228    12230    +2       
========================================  
+ Hits         11599    11601    +2       
  Misses         629      629             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2365?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2365/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://codecov.io/gh/boostorg/beast/pull/2365/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `96.72% <100.00%> (+0.05%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2365?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2365?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [55d2ceb...067d0de](https://codecov.io/gh/boostorg/beast/pull/2365?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: madmongo1  
> Created_at: 2021-12-21 14:37:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2365#issuecomment-998831095  

@vinniefalco I'd value it if you could comment on the additional wording in the documentation of websocket::stream::handshake

---
