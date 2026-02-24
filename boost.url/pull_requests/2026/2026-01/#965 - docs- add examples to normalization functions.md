# #965 docs: add examples to normalization functions [Merged]

> Username: alandefreitas  
> Created at: 2026-01-23 17:36:49 UTC  
> Updated at: 2026-01-23 21:00:17 UTC  
> Merged at: 2026-01-23 20:59:12 UTC  
> Closed at: 2026-01-23 20:59:12 UTC  
> Url: https://github.com/boostorg/url/pull/965  

fix https://github.com/boostorg/url/issues/929  
  
In passing: remove non-existent @tparam N: https://github.com/boostorg/url/issues/928

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-23 17:40:20 UTC  
> Url: https://github.com/boostorg/url/pull/965#issuecomment-3791453332  

An automated preview of the documentation is available at [https://965.url.prtest2.cppalliance.org/index.html](https://965.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-23 17:40:19 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-01-23 20:28:12 UTC  
> Url: https://github.com/boostorg/url/pull/965#issuecomment-3792260154  

GCOVR code coverage report [https://965.url.prtest2.cppalliance.org/gcovr/index.html](https://965.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://965.url.prtest2.cppalliance.org/genhtml/index.html](https://965.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff Report (in development) [https://965.url.prtest2.cppalliance.org/diff-report/index.html](https://965.url.prtest2.cppalliance.org/diff-report/index.html)  
  
Build time: 2026-01-23 20:28:11 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-01-23 20:55:59 UTC  
> Updated_at: 2026-01-23 21:00:17 UTC  
> Url: https://github.com/boostorg/url/pull/965#issuecomment-3792395165  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/965?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.35%. Comparing base ([`f4a8008`](https://app.codecov.io/gh/boostorg/url/commit/f4a80080b1012a0f0a7fb6cd472ff662fa8daefe?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`cfbd4ce`](https://app.codecov.io/gh/boostorg/url/commit/cfbd4ce6b57ab221e99d822d3b6f5fb511e0d6d5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/965/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/965?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #965   +/-   ##  
========================================  
  Coverage    99.35%   99.35%             
========================================  
  Files          161      161             
  Lines         8770     8771    +1       
========================================  
+ Hits          8713     8714    +1       
  Misses          57       57             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/965?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/router/router.hpp](https://app.codecov.io/gh/boostorg/url/pull/965?src=pr&el=tree&filepath=example%2Frouter%2Frouter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvcm91dGVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/965?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Furl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/965/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/965?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/965?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f4a8008...cfbd4ce](https://app.codecov.io/gh/boostorg/url/pull/965?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
