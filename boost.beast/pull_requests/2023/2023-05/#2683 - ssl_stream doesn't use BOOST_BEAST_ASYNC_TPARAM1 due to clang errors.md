# #2683 ssl_stream doesn't use BOOST_BEAST_ASYNC_TPARAM1 due to clang errors. [Merged]

> Username: klemens-morgenstern  
> Created at: 2023-05-12 14:40:55 UTC  
> Updated at: 2023-06-20 02:40:27 UTC  
> Merged at: 2023-06-20 02:40:21 UTC  
> Closed at: 2023-06-20 02:40:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2683  

Closes #2661

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-05-12 15:10:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2683#issuecomment-1545897131  

![pullrequest](https://2683.beast.tracing.cppalliance.org/pullrequest-beb723b1.png)  
Timeline tracing charts: [https://2683.beast.tracing.cppalliance.org/index.html](https://2683.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-05-12 15:48:23 UTC  
> Updated_at: 2023-05-12 16:01:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2683#issuecomment-1545949375  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2683?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2683](https://app.codecov.io/gh/boostorg/beast/pull/2683?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ff041a1) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/e65aff42f52f610cabb617484f8921d970f18f18?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e65aff4) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2683/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2683?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2683   +/-   ##  
========================================  
  Coverage    92.97%   92.97%             
========================================  
  Files          177      177             
  Lines        13658    13658             
========================================  
  Hits         12698    12698             
  Misses         960      960             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/beast/pull/2683?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/ssl/ssl\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2683?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9zc2wvc3NsX3N0cmVhbS5ocHA=) | `0.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2683?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2683?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e65aff4...ff041a1](https://app.codecov.io/gh/boostorg/beast/pull/2683?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2023-05-13 01:17:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2683#issuecomment-1546481332  

@vinniefalco this is technically a derivation from asio best practices for a compiler bug. I could use some input from you.

---

## Comment 4

> Username: sfjhkjdsh  
> Created_at: 2023-05-15 11:13:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2683#issuecomment-1547661239  

maybe you can have a compiler switch just for clang  
```c++  
#ifdef __clang__  
template<class AsyncStream, typename TeardownHandler>  
#else  
template<class AsyncStream, BOOST_BEAST_ASYNC_TPARAM1 TeardownHandler>  
#endif  
```

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2023-06-20 02:40:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2683#issuecomment-1598031813  

I'll merge this, because the async_teardown isn't usually called directly but by `websocket::stream::async_close`.  
  
So not having the concept check should be fine.

---
