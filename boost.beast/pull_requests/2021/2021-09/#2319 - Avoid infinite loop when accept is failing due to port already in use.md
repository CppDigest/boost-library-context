# #2319 [Http examples]Avoid infinite loop when accept is failing due to port already in use [Closed]

> Username: chreniuc  
> Created at: 2021-09-28 09:52:23 UTC  
> Updated at: 2021-11-03 21:42:43 UTC  
> Closed at: 2021-11-03 21:42:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2319  

When the port is already in use, the http server examples end up in an infinite loop and keep printing this:  
  
```  
accept: Invalid argument  
```  
  
To avoid this, we should call return if the accept fails.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-09-28 10:25:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2319#issuecomment-929060192  

![pullrequest](https://2319.beast.tracing.cppalliance.org/pullrequest-9518ca1a.png)  
Timeline tracing charts: [https://2319.beast.tracing.cppalliance.org/index.html](https://2319.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-09-28 10:50:37 UTC  
> Updated_at: 2021-09-28 11:07:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2319#issuecomment-929075896  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2319?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2319](https://codecov.io/gh/boostorg/beast/pull/2319?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (90f5a00) into [develop](https://codecov.io/gh/boostorg/beast/commit/b15a5ff0e47e72ba3d4711d2514bc65749d036ae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b15a5ff) will **decrease** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2319/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2319?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2319      +/-   ##  
===========================================  
- Coverage    94.88%   94.86%   -0.03%       
===========================================  
  Files          151      151                
  Lines        12230    12230                
===========================================  
- Hits         11605    11602       -3       
- Misses         625      628       +3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2319?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2319/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.49% <0.00%> (-0.67%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2319/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `95.61% <0.00%> (+0.87%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2319?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2319?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b15a5ff...90f5a00](https://codecov.io/gh/boostorg/beast/pull/2319?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
