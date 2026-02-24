# #2543 FreeBSD drone tests [Merged]

> Username: sdarwin  
> Created at: 2022-10-17 20:13:19 UTC  
> Updated at: 2022-12-20 10:01:13 UTC  
> Merged at: 2022-12-20 10:01:13 UTC  
> Closed at: 2022-12-20 10:01:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2543  

The freebsd tests are getting a couple of errors.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-17 20:39:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2543#issuecomment-1281467730  

![pullrequest](https://2543.beast.tracing.cppalliance.org/pullrequest-f392800a.png)  
Timeline tracing charts: [https://2543.beast.tracing.cppalliance.org/index.html](https://2543.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2022-10-18 01:59:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2543#issuecomment-1281709858  

This is an error in boost::filesystem, I got the same issue in boost.process.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2022-12-20 07:03:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2543#issuecomment-1358920387  

Interesting:  
  
```cpp  
net.inet.tcp.keepinit: 75000  
```  
  
yet the test with the timeout error fails after 274ms. So that can't be it.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-12-20 09:00:03 UTC  
> Updated_at: 2022-12-20 09:15:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2543#issuecomment-1359030759  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2543?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2543](https://codecov.io/gh/boostorg/beast/pull/2543?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5c7b2dd) into [develop](https://codecov.io/gh/boostorg/beast/commit/48f82ac817ed2712623b173793b14787ebecd153?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (48f82ac) will **decrease** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2543/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2543?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2543      +/-   ##  
===========================================  
- Coverage    94.73%   94.70%   -0.03%       
===========================================  
  Files          154      154                
  Lines        12039    12039                
===========================================  
- Hits         11405    11402       -3       
- Misses         634      637       +3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2543?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2543/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.04% <0.00%> (-0.66%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2543/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.87% <0.00%> (+0.85%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2543?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2543?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [48f82ac...5c7b2dd](https://codecov.io/gh/boostorg/beast/pull/2543?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
