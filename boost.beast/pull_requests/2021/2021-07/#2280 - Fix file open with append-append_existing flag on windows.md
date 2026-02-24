# #2280 Fix file open with append/append_existing flag on windows [Closed]

> Username: vm2mv  
> Created at: 2021-07-06 08:30:14 UTC  
> Updated at: 2021-12-10 18:08:11 UTC  
> Closed at: 2021-12-10 18:08:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2280  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-07-06 09:06:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2280#issuecomment-874592615  

![pullrequest](https://2280.beast.tracing.cppalliance.org/pullrequest-80f5181f.png)  
Timeline tracing charts: [https://2280.beast.tracing.cppalliance.org/index.html](https://2280.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-07-06 09:14:34 UTC  
> Updated_at: 2021-07-06 09:37:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2280#issuecomment-874598580  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2280?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2280](https://codecov.io/gh/boostorg/beast/pull/2280?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (211f780) into [develop](https://codecov.io/gh/boostorg/beast/commit/710cc53331f197f6f17e8c38454c09df68e43c03?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (710cc53) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2280/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2280?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2280      +/-   ##  
===========================================  
- Coverage    95.13%   95.12%   -0.01%       
===========================================  
  Files          153      153                
  Lines        11977    11973       -4       
===========================================  
- Hits         11394    11389       -5       
- Misses         583      584       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2280?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2280/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2280/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `88.23% <0.00%> (-0.23%)` | :arrow_down: |  
| [include/boost/beast/http/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2280/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `97.50% <0.00%> (-0.07%)` | :arrow_down: |  
| [include/boost/beast/core/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2280/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/beast/zlib/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2280/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2280?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2280?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [710cc53...211f780](https://codecov.io/gh/boostorg/beast/pull/2280?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-07-06 13:10:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2280#issuecomment-874746255  

Nice :) This needs a test though... @madmongo1 ?

---

## Comment 4

> Username: madmongo1  
> Created_at: 2021-07-06 13:22:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2280#issuecomment-874755793  

yep, working on it

---
