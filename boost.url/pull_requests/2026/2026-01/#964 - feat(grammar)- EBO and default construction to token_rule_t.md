# #964 feat(grammar): EBO and default construction to token_rule_t [Merged]

> Username: alandefreitas  
> Created at: 2026-01-22 23:09:26 UTC  
> Updated at: 2026-01-23 03:55:34 UTC  
> Merged at: 2026-01-23 03:54:31 UTC  
> Closed at: 2026-01-23 03:54:31 UTC  
> Url: https://github.com/boostorg/url/pull/964  

fix #936  
  
In passing: fix #937

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-22 23:15:09 UTC  
> Updated_at: 2026-01-23 00:54:53 UTC  
> Url: https://github.com/boostorg/url/pull/964#issuecomment-3787237312  

An automated preview of the documentation is available at [https://964.url.prtest2.cppalliance.org/index.html](https://964.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-23 00:54:52 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-01-22 23:24:44 UTC  
> Updated_at: 2026-01-23 01:06:19 UTC  
> Url: https://github.com/boostorg/url/pull/964#issuecomment-3787264983  

GCOVR code coverage report [https://964.url.prtest2.cppalliance.org/gcovr/index.html](https://964.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://964.url.prtest2.cppalliance.org/genhtml/index.html](https://964.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://964.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://964.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2026-01-23 01:06:18 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-01-23 03:49:22 UTC  
> Updated_at: 2026-01-23 03:55:34 UTC  
> Url: https://github.com/boostorg/url/pull/964#issuecomment-3788032874  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/964?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.35%. Comparing base ([`aef0b8b`](https://app.codecov.io/gh/boostorg/url/commit/aef0b8bbbaa3d8fef13b3d41e038c95cc341685a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`e809ee4`](https://app.codecov.io/gh/boostorg/url/commit/e809ee4c6c141ba031327ae706b74f5bb0f8a636?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/964/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/964?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #964   +/-   ##  
========================================  
  Coverage    99.34%   99.35%             
========================================  
  Files          161      161             
  Lines         8769     8770    +1       
========================================  
+ Hits          8712     8713    +1       
  Misses          57       57             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/964?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/grammar/impl/token\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/964?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Fimpl%2Ftoken_rule.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3Rva2VuX3J1bGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/grammar/token\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/964?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Ftoken_rule.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci90b2tlbl9ydWxlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/964?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/964?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [aef0b8b...e809ee4](https://app.codecov.io/gh/boostorg/url/pull/964?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
