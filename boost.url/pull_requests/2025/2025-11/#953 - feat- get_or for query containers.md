# #953 feat: get_or for query containers [Merged]

> Username: alandefreitas  
> Created at: 2025-11-13 05:11:29 UTC  
> Updated at: 2026-01-19 19:00:12 UTC  
> Merged at: 2026-01-19 18:59:57 UTC  
> Closed at: 2026-01-19 18:59:57 UTC  
> Url: https://github.com/boostorg/url/pull/953  

fix #945

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-11-13 05:18:47 UTC  
> Url: https://github.com/boostorg/url/pull/953#issuecomment-3525451693  

An automated preview of the documentation is available at [https://953.url.prtest2.cppalliance.org/index.html](https://953.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-11-13 05:18:46 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-11-13 05:30:51 UTC  
> Url: https://github.com/boostorg/url/pull/953#issuecomment-3525492957  

GCOVR code coverage report [https://953.url.prtest2.cppalliance.org/gcovr/index.html](https://953.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://953.url.prtest2.cppalliance.org/genhtml/index.html](https://953.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://953.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://953.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2025-11-13 05:30:50 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-11-13 10:34:15 UTC  
> Updated_at: 2026-01-19 19:00:12 UTC  
> Url: https://github.com/boostorg/url/pull/953#issuecomment-3527099141  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/953?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.21%. Comparing base ([`bbe9700`](https://app.codecov.io/gh/boostorg/url/commit/bbe9700fedb4897e46bc14a64de4088543054b7c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`2088d9e`](https://app.codecov.io/gh/boostorg/url/commit/2088d9e2c4f1747917624679d8b90a33ee7ccee9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/953/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/953?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #953   +/-   ##  
========================================  
  Coverage    99.21%   99.21%             
========================================  
  Files          159      159             
  Lines         8684     8705   +21       
========================================  
+ Hits          8616     8637   +21       
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/953?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/params\_encoded\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/953?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fimpl%2Fparams_encoded_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZF9iYXNlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/params\_encoded\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/953?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fparams_encoded_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX2VuY29kZWRfcmVmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/953?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fparams_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3JlZi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/953?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fparams_view.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_encoded\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/953?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fsegments_encoded_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfZW5jb2RlZF9yZWYuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/953?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fsegments_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfcmVmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/params\_base.cpp](https://app.codecov.io/gh/boostorg/url/pull/953?src=pr&el=tree&filepath=src%2Fparams_base.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3BhcmFtc19iYXNlLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/953?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/953?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bbe9700...2088d9e](https://app.codecov.io/gh/boostorg/url/pull/953?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
