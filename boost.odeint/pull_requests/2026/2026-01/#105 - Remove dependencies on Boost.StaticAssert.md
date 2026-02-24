# #105 Remove dependencies on Boost.StaticAssert [Merged]

> Username: Lastique  
> Created at: 2026-01-22 17:39:21 UTC  
> Updated at: 2026-01-23 13:27:28 UTC  
> Merged at: 2026-01-23 13:17:43 UTC  
> Closed at: 2026-01-23 13:17:43 UTC  
> Url: https://github.com/boostorg/odeint/pull/105  

Boost.StaticAssert has been merged into Boost.Config, so remove the dependency.

---

## Comment 1

> Username: mborland  
> Created_at: 2026-01-22 19:39:40 UTC  
> Updated_at: 2026-01-22 19:39:51 UTC  
> Url: https://github.com/boostorg/odeint/pull/105#issuecomment-3786337123  

I expect this one will have the same issue as test where other libs need to merge similar PRs first.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2026-01-22 20:05:17 UTC  
> Updated_at: 2026-01-23 13:18:17 UTC  
> Url: https://github.com/boostorg/odeint/pull/105#issuecomment-3786447429  

## [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/105?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.35%. Comparing base ([`3aeeaf2`](https://app.codecov.io/gh/boostorg/odeint/commit/3aeeaf229b35519013591ef90e315666a21e5067?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`6a9361a`](https://app.codecov.io/gh/boostorg/odeint/commit/6a9361a14a16449fa287b61a2258f591655cfec7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/odeint/pull/105/graphs/tree.svg?width=650&height=150&src=pr&token=Er6mj5aLOG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/odeint/pull/105?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #105   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/odeint/pull/105?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/105?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3aeeaf2...6a9361a](https://app.codecov.io/gh/boostorg/odeint/pull/105?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: Lastique  
> Created_at: 2026-01-22 20:56:53 UTC  
> Url: https://github.com/boostorg/odeint/pull/105#issuecomment-3786652307  

The problem is only with tests that manually list subdirectories to add in CMakeLists.txt. I don't see such tests in this submodule, so it should be fine.

---

## Review 4 [Approved]

> Username: mborland  
> Created_at: 2026-01-23 13:17:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/odeint/pull/105#pullrequestreview-3697434456  

Build failure unrelated

---
