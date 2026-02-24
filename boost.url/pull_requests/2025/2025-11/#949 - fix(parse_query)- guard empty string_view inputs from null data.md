# #949 fix(parse_query): guard empty string_view inputs from null data [Merged]

> Username: alandefreitas  
> Created at: 2025-11-10 18:59:01 UTC  
> Updated at: 2025-11-13 17:04:43 UTC  
> Merged at: 2025-11-13 17:04:12 UTC  
> Closed at: 2025-11-13 17:04:12 UTC  
> Url: https://github.com/boostorg/url/pull/949  

fix #946

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-11-10 19:06:01 UTC  
> Url: https://github.com/boostorg/url/pull/949#issuecomment-3513466073  

An automated preview of the documentation is available at [https://949.url.prtest2.cppalliance.org/index.html](https://949.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-11-10 19:06:00 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-11-10 19:18:30 UTC  
> Url: https://github.com/boostorg/url/pull/949#issuecomment-3513508591  

GCOVR code coverage report [https://949.url.prtest2.cppalliance.org/gcovr/index.html](https://949.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://949.url.prtest2.cppalliance.org/genhtml/index.html](https://949.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://949.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://949.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2025-11-10 19:18:28 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-11-10 19:20:57 UTC  
> Updated_at: 2025-11-13 17:04:43 UTC  
> Url: https://github.com/boostorg/url/pull/949#issuecomment-3513516909  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/949?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.21%. Comparing base ([`1e010e5`](https://app.codecov.io/gh/boostorg/url/commit/1e010e5ba102641fd5c6b997e1dae950b13faaf1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`b84ce15`](https://app.codecov.io/gh/boostorg/url/commit/b84ce156574c0d1831a93ebc2699e2150718e103?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/949/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/949?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #949   +/-   ##  
========================================  
  Coverage    99.21%   99.21%             
========================================  
  Files          159      159             
  Lines         8684     8686    +2       
========================================  
+ Hits          8616     8618    +2       
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/949?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/parse\_query.cpp](https://app.codecov.io/gh/boostorg/url/pull/949?src=pr&el=tree&filepath=src%2Fparse_query.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3BhcnNlX3F1ZXJ5LmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/949?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/949?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1e010e5...b84ce15](https://app.codecov.io/gh/boostorg/url/pull/949?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
