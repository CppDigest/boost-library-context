# #2564 Fix typo in bind_handler.hpp [Merged]

> Username: striezel  
> Created at: 2022-11-16 07:09:13 UTC  
> Updated at: 2022-12-08 15:28:01 UTC  
> Merged at: 2022-12-07 05:43:16 UTC  
> Closed at: 2022-12-07 05:43:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2564  

Closes #2563.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-16 08:37:56 UTC  
> Updated_at: 2022-11-16 08:58:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2564#issuecomment-1316602350  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2564?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2564](https://codecov.io/gh/boostorg/beast/pull/2564?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e19cffe) into [develop](https://codecov.io/gh/boostorg/beast/commit/b986b3b1b0dbd96e4426d29afa3da8f77c3b4da8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b986b3b) will **decrease** coverage by `0.04%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2564/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2564?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2564      +/-   ##  
===========================================  
- Coverage    94.73%   94.69%   -0.05%       
===========================================  
  Files          154      154                
  Lines        12027    12027                
===========================================  
- Hits         11394    11389       -5       
- Misses         633      638       +5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2564?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/2564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2JpbmRfaGFuZGxlci5ocHA=) | `66.66% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.06% <0.00%> (-0.85%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.04% <0.00%> (-0.66%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2564?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2564?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b986b3b...e19cffe](https://codecov.io/gh/boostorg/beast/pull/2564?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: striezel  
> Created_at: 2022-11-17 12:50:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2564#issuecomment-1318589581  

> ```diff  
> @@             Coverage Diff             @@  
> ##           develop    #2564      +/-   ##  
> ===========================================  
> - Coverage    94.73%   94.69%   -0.05%       
> ===========================================  
>   Files          154      154                
>   Lines        12027    12027                
> ===========================================  
> - Hits         11394    11389       -5       
> - Misses         633      638       +5       
> ```  
>   
> [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2564?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 	Coverage Δ 	  
> [include/boost/beast/core/bind_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/2564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2JpbmRfaGFuZGxlci5ocHA=) 	`66.66% <ø> (ø)` 	  
> [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) 	`94.06% <0.00%> (-0.85%)` 	arrow_down  
> [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) 	`96.04% <0.00%> (-0.66%)` 	arrow_down  
  
That is strange. How can the coverage for `include/boost/beast/websocket/impl/ping.hpp` and `include/boost/beast/websocket/impl/read.hpp` go down, even if none of those files are changed by this pull request? :thinking:  
  
In fact, this PR only changes one line in a single file, and that line is a comment, so coverage should not be affected at all.

---
