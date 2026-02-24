# #1324 Delete boost::math::is_trivial & is_trivial_v. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2025-10-01 00:07:51 UTC  
> Updated at: 2025-10-01 08:26:43 UTC  
> Merged at: 2025-10-01 08:26:27 UTC  
> Closed at: 2025-10-01 08:26:27 UTC  
> Url: https://github.com/boostorg/math/pull/1324  

`std::is_trivial` is deprecated in C++ >+ 26, and these aren't used anywhere in the internal Boost.Math code.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2025-10-01 05:36:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1324#pullrequestreview-3287625737  

Thanks! I approved the CI run, and will merge once it has cycled

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-10-01 05:39:57 UTC  
> Updated_at: 2025-10-01 08:26:43 UTC  
> Url: https://github.com/boostorg/math/pull/1324#issuecomment-3354833418  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1324?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.24%. Comparing base ([`0eacea6`](https://app.codecov.io/gh/boostorg/math/commit/0eacea69d3d5b115d7d60a03a9bd0c1f89ce0f5a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`868f959`](https://app.codecov.io/gh/boostorg/math/commit/868f9592b5ae6fab7255434f92e33f5a9b0d1d56?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1324/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1324?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1324   +/-   ##  
========================================  
  Coverage    95.24%   95.24%             
========================================  
  Files          814      814             
  Lines        69309    69309             
========================================  
  Hits         66015    66015             
  Misses        3294     3294             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1324?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1324?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0eacea6...868f959](https://app.codecov.io/gh/boostorg/math/pull/1324?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
