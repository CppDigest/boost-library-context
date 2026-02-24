# #959 fix: example router is move-only [Merged]

> Username: alandefreitas  
> Created at: 2026-01-20 17:14:15 UTC  
> Updated at: 2026-01-20 20:52:44 UTC  
> Merged at: 2026-01-20 20:52:01 UTC  
> Closed at: 2026-01-20 20:52:01 UTC  
> Url: https://github.com/boostorg/url/pull/959  

fix #956

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-20 17:20:11 UTC  
> Url: https://github.com/boostorg/url/pull/959#issuecomment-3774059334  

An automated preview of the documentation is available at [https://959.url.prtest2.cppalliance.org/index.html](https://959.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-20 17:20:10 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-01-20 17:32:55 UTC  
> Url: https://github.com/boostorg/url/pull/959#issuecomment-3774116656  

GCOVR code coverage report [https://959.url.prtest2.cppalliance.org/gcovr/index.html](https://959.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://959.url.prtest2.cppalliance.org/genhtml/index.html](https://959.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://959.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://959.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2026-01-20 17:32:54 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-01-20 17:42:29 UTC  
> Updated_at: 2026-01-20 20:52:44 UTC  
> Url: https://github.com/boostorg/url/pull/959#issuecomment-3774156781  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/959?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.34%. Comparing base ([`e618e69`](https://app.codecov.io/gh/boostorg/url/commit/e618e69fc429781b551406680884fb3badfad381?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`e2be510`](https://app.codecov.io/gh/boostorg/url/commit/e2be51083e1e8c61fab0094b876cd28e84d8a24a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/959/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/959?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #959   +/-   ##  
========================================  
  Coverage    99.34%   99.34%             
========================================  
  Files          161      161             
  Lines         8764     8764             
========================================  
  Hits          8707     8707             
  Misses          57       57             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/959?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/router/detail/router.hpp](https://app.codecov.io/gh/boostorg/url/pull/959?src=pr&el=tree&filepath=example%2Frouter%2Fdetail%2Frouter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvZGV0YWlsL3JvdXRlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [example/router/router.hpp](https://app.codecov.io/gh/boostorg/url/pull/959?src=pr&el=tree&filepath=example%2Frouter%2Frouter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvcm91dGVyLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/959?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/959?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e618e69...e2be510](https://app.codecov.io/gh/boostorg/url/pull/959?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
