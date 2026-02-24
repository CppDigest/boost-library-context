# #2577 Switched self-posting to the io-object's executor. [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-12-07 06:50:24 UTC  
> Updated at: 2022-12-20 21:09:34 UTC  
> Merged at: 2022-12-20 06:45:26 UTC  
> Closed at: 2022-12-20 06:45:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2577  

This is a conceptual change in the recent asio version that is reflected in any_completion_handler.  
  
1.82 change, as it's behaviour changing.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-12-18 08:35:24 UTC  
> Updated_at: 2022-12-18 09:15:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2577#issuecomment-1356737587  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2577?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2577](https://codecov.io/gh/boostorg/beast/pull/2577?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1dc3bc6) into [develop](https://codecov.io/gh/boostorg/beast/commit/5c48142df88d2fd5eb7a225f6cef5665814ee192?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5c48142) will **not change** coverage.  
> The diff coverage is `88.23%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2577/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2577?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2577   +/-   ##  
========================================  
  Coverage    94.70%   94.70%             
========================================  
  Files          154      154             
  Lines        12027    12027             
========================================  
  Hits         11390    11390             
  Misses         637      637             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2577?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2577/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.04% <66.66%> (ø)` | |  
| [include/boost/beast/core/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/2577/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2577/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `98.84% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.hpp](https://codecov.io/gh/boostorg/beast/pull/2577/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5ocHA=) | `96.84% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2577/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.87% <100.00%> (-0.05%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/teardown.hpp](https://codecov.io/gh/boostorg/beast/pull/2577/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5ocHA=) | `77.27% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/write.hpp](https://codecov.io/gh/boostorg/beast/pull/2577/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5ocHA=) | `98.27% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2577?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2577?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5c48142...1dc3bc6](https://codecov.io/gh/boostorg/beast/pull/2577?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-19 01:22:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2577#pullrequestreview-1222170810  

📁 include/boost/beast/core/async_base.hpp

```diff
 396 |-                     std::forward<Args>(args)...)));
 392 |+             net::post(
 393 |+                 wg1_.get_executor(),
```

> Username: vinniefalco  
> Created_at: 2022-12-19 01:22:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2577#discussion_r1051713867  

this sounds dangerously close to "wg21"


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-12-20 21:09:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2577#issuecomment-1360251830  

I have no idea if this is good or bad, because I have lost a firm understanding of executors after all the changes in Asio (plus an unhealthy dose of poor documentation). So I will have to take your word. It would be nice if there was something I could read which provides a firm understanding of the fundamentals.

---
