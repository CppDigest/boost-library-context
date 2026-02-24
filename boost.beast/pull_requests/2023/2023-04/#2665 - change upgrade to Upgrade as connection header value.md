# #2665 change upgrade to Upgrade as connection header value [Merged]

> Username: michieldwitte  
> Created at: 2023-04-01 18:21:13 UTC  
> Updated at: 2023-05-12 10:46:55 UTC  
> Merged at: 2023-05-12 10:46:55 UTC  
> Closed at: 2023-05-12 10:46:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2665  

Boost beast uses "upgrade" as the token for the "Connection" header, however rfc6455 defines otherwise.  
https://www.rfc-editor.org/rfc/rfc6455#page-18  
```  
6.   The request MUST contain a |Connection| header field whose value MUST include the "Upgrade" token.  
```  
  
Confusingly other rfc's are not consistent in using it like this.  
The server side should accept the value as case-insensitive but not all implementations honor this.  
I've had issues with this in the field (Samsung TV's for example)  
  
The other implementations I investigated all use "Upgrade".  
  
C (libwebsockets)  
https://github.com/warmcat/libwebsockets/blob/e8eb7d6bd66dde2156dbcd37b0e9048ed4006320/lib/roles/ws/client-ws.c#L168  
  
Python (websockets)  
https://github.com/aaugustin/websockets/blob/6dcac04c10c61dde9aa5be0374965c31f16e77f4/src/websockets/client.py#L118  
  
Python (websocket-client)  
https://github.com/websocket-client/websocket-client/blob/6ff442c6b11b56d98583056c4832d48f284246dc/websocket/_handshake.py#L112  
  
Node.js (WS)  
https://github.com/websockets/ws/blob/45e17acea791d865df6b255a55182e9c42e5877a/lib/websocket.js#L721

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-04-01 18:49:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2665#issuecomment-1493074138  

![pullrequest](https://2665.beast.tracing.cppalliance.org/pullrequest-32192211.png)  
Timeline tracing charts: [https://2665.beast.tracing.cppalliance.org/index.html](https://2665.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2023-04-24 02:08:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2665#issuecomment-1519281571  

Looks correct to me. I'll need to fix the CI first though, but we'll have that in the next boost release.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-05-12 08:29:59 UTC  
> Updated_at: 2023-05-12 08:45:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2665#issuecomment-1545377074  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2665?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2665](https://app.codecov.io/gh/boostorg/beast/pull/2665?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (781fb32) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/b0996f099ac4cb024675b0b320f07e2297c3130c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b0996f0) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2665/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2665?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2665   +/-   ##  
========================================  
  Coverage    92.96%   92.97%             
========================================  
  Files          177      177             
  Lines        13651    13658    +7       
========================================  
+ Hits         12691    12698    +7       
  Misses         960      960             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/beast/pull/2665?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/accept.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2665?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `97.60% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/stream\_impl.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2665?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW1faW1wbC5ocHA=) | `96.19% <100.00%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2665/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2665?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2665?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b0996f0...781fb32](https://app.codecov.io/gh/boostorg/beast/pull/2665?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
