# #94 Use x.data instead of x.begin when doing pointer arithmetic [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-09-30 18:28:49 UTC  
> Updated at: 2025-09-30 22:48:51 UTC  
> Closed at: 2025-09-30 22:45:01 UTC  
> Url: https://github.com/boostorg/odeint/pull/94  

Fixes #93

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-30 19:24:30 UTC  
> Updated_at: 2025-09-30 22:45:12 UTC  
> Url: https://github.com/boostorg/odeint/pull/94#issuecomment-3353505110  

## [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/94?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.40%. Comparing base ([`91a4a91`](https://app.codecov.io/gh/boostorg/odeint/commit/91a4a916fa193b02bda2ffc3920953387acae962?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`4641463`](https://app.codecov.io/gh/boostorg/odeint/commit/4641463fe4f9323ec76035bbbe89ea5d0c91522f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/odeint/pull/94/graphs/tree.svg?width=650&height=150&src=pr&token=Er6mj5aLOG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/odeint/pull/94?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #94      +/-   ##  
===========================================  
- Coverage    99.43%   99.40%   -0.03%       
===========================================  
  Files          168      168                
  Lines         7556     7556                
===========================================  
- Hits          7513     7511       -2       
- Misses          43       45       +2       
```  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/odeint/pull/94/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/odeint/pull/94?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/94?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [91a4a91...4641463](https://app.codecov.io/gh/boostorg/odeint/pull/94?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: LegalizeAdulthood  
> Created_at: 2025-09-30 22:45:01 UTC  
> Url: https://github.com/boostorg/odeint/pull/94#issuecomment-3354027875  

the authorship on the commits is wrong, so I'm closing this and will open a new PR with the correct information

---
