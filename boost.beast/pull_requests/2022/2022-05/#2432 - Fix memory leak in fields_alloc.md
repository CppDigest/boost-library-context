# #2432 Fix memory leak in fields_alloc [Closed]

> Username: deW1  
> Created at: 2022-05-22 01:11:52 UTC  
> Updated at: 2022-05-22 05:12:07 UTC  
> Closed at: 2022-05-22 05:12:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2432  

This fixes Issue #2431

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-22 01:15:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2432#pullrequestreview-980888317  

📁 example/http/server/fast/fields_alloc.hpp

```diff
  57 |     destroy()
  58 |     {
  59 |-         if(refs_--)
```

> Username: vinniefalco  
> Created_at: 2022-05-22 01:15:20 UTC  
> Updated_at: 2022-05-22 01:15:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2432#discussion_r878778558  

short and sweet


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-05-22 02:03:23 UTC  
> Updated_at: 2022-05-22 02:20:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2432#issuecomment-1133801392  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2432?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2432](https://codecov.io/gh/boostorg/beast/pull/2432?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7fddc3b) into [develop](https://codecov.io/gh/boostorg/beast/commit/9d23bec2bc523223a377582302bd28d04cb514ed?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9d23bec) will **decrease** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2432/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2432?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2432      +/-   ##  
===========================================  
- Coverage    94.69%   94.67%   -0.03%       
===========================================  
  Files          149      149                
  Lines        11773    11773                
===========================================  
- Hits         11149    11146       -3       
- Misses         624      627       +3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2432?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2432/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.50% <0.00%> (-0.67%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2432/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `95.61% <0.00%> (+0.87%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2432?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2432?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9d23bec...7fddc3b](https://codecov.io/gh/boostorg/beast/pull/2432?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
