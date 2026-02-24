# #2532 Added logic to explicitly close TCP socket for sync HTTPS client [Closed]

> Username: pavel-odintsov  
> Created at: 2022-10-09 17:45:02 UTC  
> Updated at: 2022-10-10 13:11:42 UTC  
> Closed at: 2022-10-10 13:11:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2532  

Hello!  
  
I noticed that example client does not include logic which explicitly closes TCP socket, I think it's good ideas to add it here.  
  
In addition to leaked socket I hit quite nasty issue when my CDN (Fastly) does not implement proper TLS termination and shutdown() hands indefinitely. So I've added comment about this case too.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-09 18:12:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2532#issuecomment-1272598733  

![pullrequest](https://2532.beast.tracing.cppalliance.org/pullrequest-2ba3eeab.png)  
Timeline tracing charts: [https://2532.beast.tracing.cppalliance.org/index.html](https://2532.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-09 18:33:01 UTC  
> Updated_at: 2022-10-09 18:40:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2532#issuecomment-1272602728  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2532?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2532](https://codecov.io/gh/boostorg/beast/pull/2532?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6aed48c) into [develop](https://codecov.io/gh/boostorg/beast/commit/541a6bf4538422f0e274f17ae2ac6d871aaf78b4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (541a6bf) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2532/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2532?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2532      +/-   ##  
===========================================  
- Coverage    94.72%   94.68%   -0.04%       
===========================================  
  Files          152      152                
  Lines        11882    11882                
===========================================  
- Hits         11255    11251       -4       
- Misses         627      631       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2532?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2532/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.49% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2532?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2532?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [541a6bf...6aed48c](https://codecov.io/gh/boostorg/beast/pull/2532?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-09 19:22:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2532#pullrequestreview-1135378763  

📁 example/http/client/sync-ssl/http_client_sync_ssl.cpp

```diff
 124 |+ 
 125 |+         // As final step we need to close underlying TCP connection
 126 |+         beast::get_lowest_layer(stream).socket().shutdown( boost::asio::socket_base::shutdown_both, ec);
```

> Username: vinniefalco  
> Created_at: 2022-10-09 19:22:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2532#discussion_r990828509  

well this might need to be added to ALL the SSL clients :) but read this:  
  
http://www.serverframework.com/asynchronousevents/2011/01/time-wait-and-its-design-implications-for-protocols-and-scalable-servers.html


---

## Comment 4

> Username: pavel-odintsov  
> Created_at: 2022-10-10 10:57:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2532#issuecomment-1273136320  

I had conversation with my friend from OpenSSL and I think this issue will need more investigation on my side. I use old OpenSSL (1.0) and it may be root cause of such behaviour. I'll repeat my tests with fresh OpenSSL and will report back.

---

## Comment 5

> Username: pavel-odintsov  
> Created_at: 2022-10-10 13:11:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2532#issuecomment-1273293591  

After upgrade to OpenSSL 1.1.1q shutdown works fine and does not block execution. I can confirm that old OpenSSL (1.0.2d) was root cause.  
  
Socket close is not needed too, shutdown moves socket to TIME_WAIT status which is perfectly fine.

---
