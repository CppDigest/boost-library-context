# #1136 Update CI [Merged]

> Username: mborland  
> Created at: 2024-05-21 06:47:20 UTC  
> Updated at: 2024-05-23 10:56:49 UTC  
> Merged at: 2024-05-23 10:56:45 UTC  
> Closed at: 2024-05-23 10:56:45 UTC  
> Url: https://github.com/boostorg/math/pull/1136  

Removes MSVC 14.0 and adds clangs 15, 16, 17, 18 as well as GCC 14. Update all drone sanitizers to latest and greatest.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-21 10:13:23 UTC  
> Updated_at: 2024-05-23 10:42:06 UTC  
> Url: https://github.com/boostorg/math/pull/1136#issuecomment-2122273540  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1136?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.71%. Comparing base [(`52be1ad`)](https://app.codecov.io/gh/boostorg/math/commit/52be1ad7f203945b874078e969703b105dceeba5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1ec1661`)](https://app.codecov.io/gh/boostorg/math/pull/1136?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1136/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1136?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1136   +/-   ##  
========================================  
  Coverage    93.71%   93.71%             
========================================  
  Files          771      772    +1       
  Lines        61166    61168    +2       
========================================  
+ Hits         57321    57323    +2       
  Misses        3845     3845             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1136?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_autodiff\_5.cpp](https://app.codecov.io/gh/boostorg/math/pull/1136?src=pr&el=tree&filepath=test%2Ftest_autodiff_5.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2F1dG9kaWZmXzUuY3Bw) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1136/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1136?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1136?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [52be1ad...1ec1661](https://app.codecov.io/gh/boostorg/math/pull/1136?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-05-22 06:29:39 UTC  
> Updated_at: 2024-05-23 09:02:07 UTC  
> Url: https://github.com/boostorg/math/pull/1136#issuecomment-2123967976  

@jzmaddock is [this failure](https://github.com/boostorg/math/actions/runs/9171166088/job/25215137805?pr=1136#step:11:619) on macOS unexpected? I didn't see it listed in https://github.com/boostorg/math/issues/1129  
  
Edit: I see it failing on develop so no

---
