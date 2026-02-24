# #2556 Tests adopting asio code changes for asio 1.25.0. [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-10-31 18:48:37 UTC  
> Updated at: 2022-11-01 06:45:54 UTC  
> Merged at: 2022-11-01 06:45:54 UTC  
> Closed at: 2022-11-01 06:45:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2556  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-01 04:19:39 UTC  
> Updated_at: 2022-11-01 05:08:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2556#issuecomment-1298007671  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2556?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2556](https://codecov.io/gh/boostorg/beast/pull/2556?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6971380) into [develop](https://codecov.io/gh/boostorg/beast/commit/62e1b0e2fe5e0145da6c2f47e03f256e27d05318?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (62e1b0e) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2556/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2556?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2556      +/-   ##  
===========================================  
- Coverage    94.72%   94.69%   -0.04%       
===========================================  
  Files          154      154                
  Lines        12022    12027       +5       
===========================================  
+ Hits         11388    11389       +1       
- Misses         634      638       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2556?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/work\_guard.hpp](https://codecov.io/gh/boostorg/beast/pull/2556/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC93b3JrX2d1YXJkLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/stream\_traits.hpp](https://codecov.io/gh/boostorg/beast/pull/2556/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmVhbV90cmFpdHMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2556/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.04% <0.00%> (-0.66%)` | :arrow_down: |  
| [include/boost/beast/http/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2556/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `98.83% <0.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/beast/http/impl/write.hpp](https://codecov.io/gh/boostorg/beast/pull/2556/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `92.14% <0.00%> (+0.12%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/teardown.hpp](https://codecov.io/gh/boostorg/beast/pull/2556/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5ocHA=) | `77.27% <0.00%> (+0.34%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2556?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2556?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4a8ced4...6971380](https://codecov.io/gh/boostorg/beast/pull/2556?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
