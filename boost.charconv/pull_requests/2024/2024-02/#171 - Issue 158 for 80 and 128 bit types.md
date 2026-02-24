# #171 Issue 158 for 80 and 128 bit types [Merged]

> Username: mborland  
> Created at: 2024-02-22 08:59:29 UTC  
> Updated at: 2024-02-22 09:55:19 UTC  
> Merged at: 2024-02-22 09:55:16 UTC  
> Closed at: 2024-02-22 09:55:16 UTC  
> Url: https://github.com/boostorg/charconv/pull/171  

Fix precision differences brought up in #158 and apply similar fixes. This is incremental improvement, and can sometimes round differently than libstdc++ does.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-22 09:37:50 UTC  
> Updated_at: 2024-02-22 09:50:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/171#issuecomment-1959052007  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `2 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`317e04f`)](https://app.codecov.io/gh/boostorg/charconv/commit/317e04fefff952c115fa27572c26d176f7942912?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.13% compared to head [(`91729ec`)](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.29%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/171/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #171      +/-   ##  
===========================================  
+ Coverage    93.13%   93.29%   +0.15%       
===========================================  
  Files           65       65                
  Lines         8506     8571      +65       
===========================================  
+ Hits          7922     7996      +74       
+ Misses         584      575       -9       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/github\_issue\_158.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTU4LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/to\_chars\_float\_STL\_comp.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFyc19mbG9hdF9TVExfY29tcC5jcHA=) | `100.00% <ø> (ø)` | |  
| [...lude/boost/charconv/detail/ryu/ryu\_generic\_128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L3J5dV9nZW5lcmljXzEyOC5ocHA=) | `87.86% <91.66%> (+4.23%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/charconv/pull/171/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [317e04f...91729ec](https://app.codecov.io/gh/boostorg/charconv/pull/171?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
