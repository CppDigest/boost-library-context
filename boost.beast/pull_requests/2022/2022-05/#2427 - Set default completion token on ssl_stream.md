# #2427 Set default completion token on ssl_stream [Closed]

> Username: ecatmur  
> Created at: 2022-05-17 15:43:17 UTC  
> Updated at: 2022-05-17 16:59:29 UTC  
> Closed at: 2022-05-17 16:58:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2427  

async methods

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-05-17 16:09:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2427#issuecomment-1129057635  

![pullrequest](https://2427.beast.tracing.cppalliance.org/pullrequest-97d805bc.png)  
Timeline tracing charts: [https://2427.beast.tracing.cppalliance.org/index.html](https://2427.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2022-05-17 16:13:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2427#issuecomment-1129061317  

Yep, this looks right. However, did Chris enable move-construction for `ssl::stream` in the latest Asio? If so, then we might not even need this class at all.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-05-17 16:36:56 UTC  
> Updated_at: 2022-05-17 16:39:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2427#issuecomment-1129085050  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2427?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2427](https://codecov.io/gh/boostorg/beast/pull/2427?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7aad4d4) into [develop](https://codecov.io/gh/boostorg/beast/commit/9d23bec2bc523223a377582302bd28d04cb514ed?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9d23bec) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2427/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2427?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2427      +/-   ##  
===========================================  
- Coverage    94.69%   94.66%   -0.04%       
===========================================  
  Files          149      149                
  Lines        11773    11773                
===========================================  
- Hits         11149    11145       -4       
- Misses         624      628       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2427?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2427/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.50% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2427?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2427?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9d23bec...7aad4d4](https://codecov.io/gh/boostorg/beast/pull/2427?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 4

> Username: ecatmur  
> Created_at: 2022-05-17 16:58:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2427#issuecomment-1129104628  

Ah yeah, it's been move constructible since 1.74 and move assignable since 1.77. And scatter-gather got into 1.74 as well.  
Replacing `boost::beast::ssl_stream` with `boost::asio::ssl::stream` looks good in my code, PR to deprecate?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-05-17 16:59:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2427#issuecomment-1129105277  

PR to deprecate, yes please

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-05-17 16:59:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2427#issuecomment-1129105415  

Scatter-gather, NICE !!!

---
