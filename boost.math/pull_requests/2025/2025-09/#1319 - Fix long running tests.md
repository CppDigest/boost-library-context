# #1319 Fix long running tests [Merged]

> Username: demroz  
> Created at: 2025-09-13 13:20:07 UTC  
> Updated at: 2025-09-18 05:42:11 UTC  
> Merged at: 2025-09-18 05:41:57 UTC  
> Closed at: 2025-09-18 05:41:57 UTC  
> Url: https://github.com/boostorg/math/pull/1319  

This pr should fix the failures in the long running tests for reverse mode autodiff and adjust the tolerances on the regular tests.  
cc: @mborland

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-14 16:38:32 UTC  
> Updated_at: 2025-09-18 05:42:11 UTC  
> Url: https://github.com/boostorg/math/pull/1319#issuecomment-3289675950  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1319?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.11%. Comparing base ([`7535a6e`](https://app.codecov.io/gh/boostorg/math/commit/7535a6ef2331ffb19d2abd166814e0ff986f1b38?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`811fa5a`](https://app.codecov.io/gh/boostorg/math/commit/811fa5aff75e7347dc9ce6a4c40a0452e744d4ef?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 53 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1319/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1319?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1319   +/-   ##  
========================================  
  Coverage    95.11%   95.11%             
========================================  
  Files          797      797             
  Lines        67197    67197             
========================================  
  Hits         63912    63912             
  Misses        3285     3285             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1319?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1319?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7535a6e...811fa5a](https://app.codecov.io/gh/boostorg/math/pull/1319?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2025-09-17 08:27:18 UTC  
> Url: https://github.com/boostorg/math/pull/1319#issuecomment-3301903080  

@demroz Your tree was missing the commit with the long running test runners, so I cherry picked it for you. Everything else seemed fine to me.

---

## Comment 3

> Username: demroz  
> Created_at: 2025-09-17 16:54:09 UTC  
> Url: https://github.com/boostorg/math/pull/1319#issuecomment-3303835504  

@mborland looks like the c++23 types are not playing nicely with these tests. I'll look into it

---

## Comment 4

> Username: demroz  
> Created_at: 2025-09-17 19:57:12 UTC  
> Url: https://github.com/boostorg/math/pull/1319#issuecomment-3304385458  

should be all good now

---
