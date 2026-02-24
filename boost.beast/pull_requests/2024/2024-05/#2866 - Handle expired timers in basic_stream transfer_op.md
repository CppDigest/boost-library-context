# #2866 Handle expired timers in basic_stream transfer_op [Merged]

> Username: ashtum  
> Created at: 2024-05-13 07:45:45 UTC  
> Updated at: 2024-05-13 18:47:17 UTC  
> Merged at: 2024-05-13 18:47:17 UTC  
> Closed at: 2024-05-13 18:47:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2866  

Previously, the code only handled expired timers when the buffer was empty.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-13 08:50:10 UTC  
> Updated_at: 2024-05-13 08:54:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2866#issuecomment-2107004507  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2866?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.02%. Comparing base [(`95f0cfb`)](https://app.codecov.io/gh/boostorg/beast/commit/95f0cfb92621736c74025e439c2d51b063832a51?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ff3244a`)](https://app.codecov.io/gh/boostorg/beast/pull/2866?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2866/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2866?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2866      +/-   ##  
===========================================  
- Coverage    93.05%   93.02%   -0.04%       
===========================================  
  Files          178      178                
  Lines        13688    13694       +6       
===========================================  
+ Hits         12738    12739       +1       
- Misses         950      955       +5       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2866?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/stream\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2866?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fdetail%2Fstream_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9zdHJlYW1fYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/core/impl/basic\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2866?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fbasic_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYmFzaWNfc3RyZWFtLmhwcA==) | `85.61% <100.00%> (-0.50%)` | :arrow_down: |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2866/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2866?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2866?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [95f0cfb...ff3244a](https://app.codecov.io/gh/boostorg/beast/pull/2866?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: ashtum  
> Created_at: 2024-05-13 09:11:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2866#pullrequestreview-2052128063  

📁 include/boost/beast/core/impl/basic_stream.hpp

```diff
 346 | 
 347 |                     async_perform(0, is_read{});
```

> Username: ashtum  
> Created_at: 2024-05-13 09:11:11 UTC  
> Updated_at: 2024-05-13 09:11:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2866#discussion_r1598135563  

@vinniefalco I believe the use of `async_perform(0, is_read{})` here has been to reduce the number of template instantiations. If that's the case, I can replace the use of `net::dispatch` with `async_perform(0, is_read{})` for the same reason.

> Username: vinniefalco  
> Created_at: 2024-05-13 17:07:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2866#discussion_r1598790797  

seems OK I guess...I think you know more about this than me now.


---
