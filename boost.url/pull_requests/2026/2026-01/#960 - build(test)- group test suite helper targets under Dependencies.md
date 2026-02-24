# #960 build(test): group test suite helper targets under Dependencies [Merged]

> Username: alandefreitas  
> Created at: 2026-01-20 21:14:38 UTC  
> Updated at: 2026-01-21 17:04:11 UTC  
> Merged at: 2026-01-21 17:02:38 UTC  
> Closed at: 2026-01-21 17:02:38 UTC  
> Url: https://github.com/boostorg/url/pull/960  

The test suite exposes two helper libraries (with and without a main), as with other test libraries, so other modules can choose their own entry point. Keeping both targets is necessary, but they clutter Visual Studio’s solution view.  
  
Move these helper targets into the existing Dependencies folder to reduce noise while preserving the separate-linking behavior.  
  
fix #955

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-20 21:20:05 UTC  
> Url: https://github.com/boostorg/url/pull/960#issuecomment-3775004097  

An automated preview of the documentation is available at [https://960.url.prtest2.cppalliance.org/index.html](https://960.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-20 21:20:04 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-01-20 21:34:57 UTC  
> Url: https://github.com/boostorg/url/pull/960#issuecomment-3775055346  

GCOVR code coverage report [https://960.url.prtest2.cppalliance.org/gcovr/index.html](https://960.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://960.url.prtest2.cppalliance.org/genhtml/index.html](https://960.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://960.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://960.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2026-01-20 21:34:56 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-01-20 21:45:23 UTC  
> Updated_at: 2026-01-21 17:04:11 UTC  
> Url: https://github.com/boostorg/url/pull/960#issuecomment-3775092013  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/960?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.34%. Comparing base ([`161cf73`](https://app.codecov.io/gh/boostorg/url/commit/161cf7331121c0cb58da4d0bab29ea4a47110c80?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`e085062`](https://app.codecov.io/gh/boostorg/url/commit/e085062390fd2a953de501fa9f0ff80cdddf2c24?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/960/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/960?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #960   +/-   ##  
========================================  
  Coverage    99.34%   99.34%             
========================================  
  Files          161      161             
  Lines         8764     8764             
========================================  
  Hits          8707     8707             
  Misses          57       57             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/960?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/960?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [161cf73...e085062](https://app.codecov.io/gh/boostorg/url/pull/960?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
