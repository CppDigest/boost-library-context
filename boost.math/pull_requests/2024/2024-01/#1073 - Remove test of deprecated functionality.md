# #1073 Remove test of deprecated functionality [Closed]

> Username: NAThompson  
> Created at: 2024-01-24 21:08:22 UTC  
> Updated at: 2024-01-26 21:22:25 UTC  
> Closed at: 2024-01-26 18:57:19 UTC  
> Url: https://github.com/boostorg/math/pull/1073  

The CI build logs are getting hard to read. Slowly chip away at the warnings emitted.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2024-01-24 21:12:13 UTC  
> Url: https://github.com/boostorg/math/pull/1073#issuecomment-1908922998  

@jzmaddock : Always debatable whether we want to remove deprecated functionality from the CI tests; I'll let you decide on whether to merge or close.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-01-24 22:23:33 UTC  
> Updated_at: 2024-01-25 00:26:15 UTC  
> Url: https://github.com/boostorg/math/pull/1073#issuecomment-1909012002  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1073?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`31b76ee`)](https://app.codecov.io/gh/boostorg/math/commit/31b76ee734ffd842b8dff5e8d6d7c0c2e5d2007d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.22% compared to head [(`97726ac`)](https://app.codecov.io/gh/boostorg/math/pull/1073?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.30%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1073/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1073?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1073      +/-   ##  
===========================================  
+ Coverage    85.22%   85.30%   +0.07%       
===========================================  
  Files          877      871       -6       
  Lines        66819    66771      -48       
===========================================  
+ Hits         56948    56956       +8       
+ Misses        9871     9815      -56       
```  
  
  
[see 2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1073/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1073?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1073?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [31b76ee...97726ac](https://app.codecov.io/gh/boostorg/math/pull/1073?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: mborland  
> Created_at: 2024-01-25 07:10:57 UTC  
> Url: https://github.com/boostorg/math/pull/1073#issuecomment-1909475817  

I'd vote leave the testing until we remove the functionality.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-01-26 19:33:16 UTC  
> Url: https://github.com/boostorg/math/pull/1073#issuecomment-1912587162  

Sorry, missed this, I agree with Matt, if we provide we should test it, deprecated or not.

---
