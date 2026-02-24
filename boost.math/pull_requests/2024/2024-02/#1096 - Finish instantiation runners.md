# #1096 Finish instantiation runners [Merged]

> Username: ckormanyos  
> Created at: 2024-02-17 10:13:23 UTC  
> Updated at: 2024-02-17 12:12:44 UTC  
> Merged at: 2024-02-17 12:08:15 UTC  
> Closed at: 2024-02-17 12:08:15 UTC  
> Url: https://github.com/boostorg/math/pull/1096  

Fishish instantiation runner work started in #1094 and #1095

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-17 12:07:03 UTC  
> Url: https://github.com/boostorg/math/pull/1096#issuecomment-1950002637  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1096?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`5b8569c`)](https://app.codecov.io/gh/boostorg/math/commit/5b8569c78ed5312940cc982db5a6a5e616cf30d2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 91.86% compared to head [(`b0c4224`)](https://app.codecov.io/gh/boostorg/math/pull/1096?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 92.61%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1096/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1096?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1096      +/-   ##  
===========================================  
+ Coverage    91.86%   92.61%   +0.75%       
===========================================  
  Files          769      769                
  Lines        63831    63883      +52       
===========================================  
+ Hits         58637    59164     +527       
+ Misses        5194     4719     -475       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1096?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/compile\_test/instantiate.hpp](https://app.codecov.io/gh/boostorg/math/pull/1096?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jb21waWxlX3Rlc3QvaW5zdGFudGlhdGUuaHBw) | `100.00% <100.00%> (+33.24%)` | :arrow_up: |  
| [test/test\_instantiate3.cpp](https://app.codecov.io/gh/boostorg/math/pull/1096?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2luc3RhbnRpYXRlMy5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1096/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1096?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1096?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5b8569c...b0c4224](https://app.codecov.io/gh/boostorg/math/pull/1096?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-02-17 12:11:15 UTC  
> Updated_at: 2024-02-17 12:12:06 UTC  
> Url: https://github.com/boostorg/math/pull/1096#issuecomment-1950010568  

OK. This preliminarily wraps up the topic of getting _instantiations_ actually running (in addition to simply compiling).  
  
I'll probably ramp down with the trouble-making for the ramainder of the road to 1.85 (unless something broken or mysterious with my recent work comes up).  
  
If anyone is following the action on CodeCov, we now have a solid $92{\ldots}93\\%$.  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2024-02-17 12:12:42 UTC  
> Url: https://github.com/boostorg/math/pull/1096#issuecomment-1950013458  

See also #1068

---
