# #98 Add using declarations to bring placeholders _1, _2 into scope [Merged]

> Username: LegalizeAdulthood  
> Created at: 2025-09-30 22:00:10 UTC  
> Updated at: 2025-12-01 18:05:39 UTC  
> Merged at: 2025-12-01 18:04:48 UTC  
> Closed at: 2025-12-01 18:04:48 UTC  
> Url: https://github.com/boostorg/odeint/pull/98  

Fixes #97

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-01 14:45:02 UTC  
> Updated_at: 2025-12-01 18:05:39 UTC  
> Url: https://github.com/boostorg/odeint/pull/98#issuecomment-3356780007  

## [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/98?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.35%. Comparing base ([`4ea4636`](https://app.codecov.io/gh/boostorg/odeint/commit/4ea46366d5c51fa6f74b9b77654279ed1d11725e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`40a2c22`](https://app.codecov.io/gh/boostorg/odeint/commit/40a2c22e26d26cef2f71d701cf96b4aacc950e8b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/odeint/pull/98/graphs/tree.svg?width=650&height=150&src=pr&token=Er6mj5aLOG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/odeint/pull/98?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #98   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/odeint/pull/98?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/98?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4ea4636...40a2c22](https://app.codecov.io/gh/boostorg/odeint/pull/98?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: LegalizeAdulthood  
> Created_at: 2025-10-06 15:08:19 UTC  
> Url: https://github.com/boostorg/odeint/pull/98#issuecomment-3372201110  

The failing CI jobs are failing on the checkout action.  They aren't even getting my code.  Same for the other open PRs I've submitted.

---

## Review 3 [Approved]

> Username: mborland  
> Created_at: 2025-12-01 11:24:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/odeint/pull/98#pullrequestreview-3524488574  

LGTM. Will rebase and merge once CI is fixed in #104

---
