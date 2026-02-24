# #2256 Consolidate drone configs [Closed]

> Username: sdarwin  
> Created at: 2021-06-10 18:22:23 UTC  
> Updated at: 2021-08-04 09:09:31 UTC  
> Closed at: 2021-08-04 09:09:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2256  

Reduce total number of drone config files. Include more drone tests.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-06-10 18:57:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2256#issuecomment-858915801  

![pullrequest](https://2256.beast.tracing.cppalliance.org/pullrequest-ba14b768.png)  
Timeline tracing charts: [https://2256.beast.tracing.cppalliance.org/index.html](https://2256.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-06-10 19:19:33 UTC  
> Updated_at: 2021-06-10 19:40:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2256#issuecomment-858944524  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2256?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2256](https://codecov.io/gh/boostorg/beast/pull/2256?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7215e2c) into [develop](https://codecov.io/gh/boostorg/beast/commit/710cc53331f197f6f17e8c38454c09df68e43c03?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (710cc53) will **decrease** coverage by `0.26%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2256/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2256?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2256      +/-   ##  
===========================================  
- Coverage    95.13%   94.86%   -0.27%       
===========================================  
  Files          153      151       -2       
  Lines        11977    12291     +314       
===========================================  
+ Hits         11394    11660     +266       
- Misses         583      631      +48       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2256?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2JpbmRfaGFuZGxlci5ocHA=) | `66.66% <0.00%> (-33.34%)` | :arrow_down: |  
| [include/boost/beast/http/serializer.hpp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `90.90% <0.00%> (-9.10%)` | :arrow_down: |  
| [include/boost/beast/http/impl/serializer.hpp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5ocHA=) | `91.76% <0.00%> (-2.71%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.73% <0.00%> (-2.59%)` | :arrow_down: |  
| [include/boost/beast/http/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `95.23% <0.00%> (-2.33%)` | :arrow_down: |  
| [include/boost/beast/core/impl/buffers\_prefix.hpp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19wcmVmaXguaHBw) | `98.05% <0.00%> (-1.95%)` | :arrow_down: |  
| [include/boost/beast/core/rate\_policy.hpp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3JhdGVfcG9saWN5LmhwcA==) | `80.00% <0.00%> (-1.82%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `96.66% <0.00%> (-1.60%)` | :arrow_down: |  
| [include/boost/beast/http/basic\_file\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2ZpbGVfYm9keS5ocHA=) | `81.81% <0.00%> (-1.52%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/accept.hpp](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `98.59% <0.00%> (-1.41%)` | :arrow_down: |  
| ... and [98 more](https://codecov.io/gh/boostorg/beast/pull/2256/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2256?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2256?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [710cc53...7215e2c](https://codecov.io/gh/boostorg/beast/pull/2256?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-07-19 17:48:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2256#issuecomment-882739672  

Is this good to go?

---

## Comment 4

> Username: sdarwin  
> Created_at: 2021-07-28 21:16:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2256#issuecomment-888627096  

> Is this good to go?  
  
@madmongo1 is welcome to review.  At the time of submitting the PR, all tests had passed, ready to merge: https://drone.cpp.al/boostorg/beast/137

---

## Comment 5

> Username: madmongo1  
> Created_at: 2021-07-29 15:19:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2256#issuecomment-889235532  

Yep, happy to accept

---

## Comment 6

> Username: madmongo1  
> Created_at: 2021-08-04 09:09:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2256#issuecomment-892496507  

Rebased to develop

---
