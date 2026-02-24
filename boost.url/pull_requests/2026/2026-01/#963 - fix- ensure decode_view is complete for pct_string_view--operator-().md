# #963 fix: ensure decode_view is complete for pct_string_view::operator*() [Merged]

> Username: alandefreitas  
> Created at: 2026-01-22 18:36:30 UTC  
> Updated at: 2026-01-22 22:06:00 UTC  
> Merged at: 2026-01-22 21:56:19 UTC  
> Closed at: 2026-01-22 21:56:19 UTC  
> Url: https://github.com/boostorg/url/pull/963  

fix #937

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-22 18:40:23 UTC  
> Url: https://github.com/boostorg/url/pull/963#issuecomment-3786074335  

An automated preview of the documentation is available at [https://963.url.prtest2.cppalliance.org/index.html](https://963.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-22 18:40:22 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-01-22 18:54:05 UTC  
> Url: https://github.com/boostorg/url/pull/963#issuecomment-3786131086  

GCOVR code coverage report [https://963.url.prtest2.cppalliance.org/gcovr/index.html](https://963.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://963.url.prtest2.cppalliance.org/genhtml/index.html](https://963.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://963.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://963.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2026-01-22 18:54:04 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-01-22 22:02:52 UTC  
> Updated_at: 2026-01-22 22:06:00 UTC  
> Url: https://github.com/boostorg/url/pull/963#issuecomment-3786977080  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/963?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.34%. Comparing base ([`3228399`](https://app.codecov.io/gh/boostorg/url/commit/32283999e8d1b6bb66cb4d98eca495d6627b3290?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`01e0571`](https://app.codecov.io/gh/boostorg/url/commit/01e0571b0b486070465e7fb1421dd5fd043e7349?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/963/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/963?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #963   +/-   ##  
========================================  
  Coverage    99.34%   99.34%             
========================================  
  Files          161      161             
  Lines         8769     8769             
========================================  
  Hits          8712     8712             
  Misses          57       57             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/963?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/pct\_string\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/963?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fpct_string_view.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X3N0cmluZ192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/963?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/963?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3228399...01e0571](https://app.codecov.io/gh/boostorg/url/pull/963?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
