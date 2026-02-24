# #101 Use x.data instead of x.begin when doing pointer arithmetic [Merged]

> Username: LegalizeAdulthood  
> Created at: 2025-09-30 22:50:53 UTC  
> Updated at: 2025-12-02 10:08:39 UTC  
> Merged at: 2025-12-02 10:08:18 UTC  
> Closed at: 2025-12-02 10:08:18 UTC  
> Url: https://github.com/boostorg/odeint/pull/101  

Fixes #93

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-01 14:45:08 UTC  
> Updated_at: 2025-12-02 10:08:39 UTC  
> Url: https://github.com/boostorg/odeint/pull/101#issuecomment-3356780456  

## [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/101?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.35%. Comparing base ([`2875284`](https://app.codecov.io/gh/boostorg/odeint/commit/2875284ab6a087ba98bedbc4fed4d55b3e2a6d2a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`d577f19`](https://app.codecov.io/gh/boostorg/odeint/commit/d577f19609e5ae36ae36355e1237550aca492623?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/odeint/pull/101/graphs/tree.svg?width=650&height=150&src=pr&token=Er6mj5aLOG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/odeint/pull/101?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #101   +/-   ##  
========================================  
  Coverage    99.35%   99.35%             
========================================  
  Files          168      168             
  Lines         6833     6833             
========================================  
  Hits          6789     6789             
  Misses          44       44             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/odeint/pull/101?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/101?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2875284...d577f19](https://app.codecov.io/gh/boostorg/odeint/pull/101?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
