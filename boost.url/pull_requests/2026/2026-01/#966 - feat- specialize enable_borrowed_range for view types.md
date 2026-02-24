# #966 feat: specialize enable_borrowed_range for view types [Merged]

> Username: alandefreitas  
> Created at: 2026-01-23 22:37:55 UTC  
> Updated at: 2026-01-26 17:03:20 UTC  
> Merged at: 2026-01-26 17:01:22 UTC  
> Closed at: 2026-01-26 17:01:22 UTC  
> Url: https://github.com/boostorg/url/pull/966  

All view types in Boost.URL have iterators that store raw pointers to external data rather than references to the view object itself. This means iterators remain valid even after the view is destroyed.  
  
fix #927

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-23 22:44:54 UTC  
> Updated_at: 2026-01-23 23:04:59 UTC  
> Url: https://github.com/boostorg/url/pull/966#issuecomment-3792903406  

An automated preview of the documentation is available at [https://966.url.prtest2.cppalliance.org/index.html](https://966.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-23 23:04:58 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-01-23 22:53:22 UTC  
> Updated_at: 2026-01-23 23:15:16 UTC  
> Url: https://github.com/boostorg/url/pull/966#issuecomment-3792928215  

GCOVR code coverage report [https://966.url.prtest2.cppalliance.org/gcovr/index.html](https://966.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://966.url.prtest2.cppalliance.org/genhtml/index.html](https://966.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff Report (in development) [https://966.url.prtest2.cppalliance.org/diff-report/index.html](https://966.url.prtest2.cppalliance.org/diff-report/index.html)  
  
Build time: 2026-01-23 23:15:14 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-01-23 23:29:50 UTC  
> Updated_at: 2026-01-26 17:03:20 UTC  
> Url: https://github.com/boostorg/url/pull/966#issuecomment-3793006318  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/966?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.35%. Comparing base ([`4c6d357`](https://app.codecov.io/gh/boostorg/url/commit/4c6d357f7a88cbc9ef5af48806f5cd7f8366111a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`e7325b2`](https://app.codecov.io/gh/boostorg/url/commit/e7325b2224c312215d287e03eb54e1d2162e3edd?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/966/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/966?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #966   +/-   ##  
========================================  
  Coverage    99.35%   99.35%             
========================================  
  Files          161      161             
  Lines         8771     8771             
========================================  
  Hits          8714     8714             
  Misses          57       57             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/966?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/decode\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/966?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdecode_view.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_encoded\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/966?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fparams_encoded_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX2VuY29kZWRfcmVmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/966?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fparams_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3JlZi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/966?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fparams_view.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/pct\_string\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/966?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fpct_string_view.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X3N0cmluZ192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_encoded\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/966?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fsegments_encoded_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfZW5jb2RlZF9yZWYuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/966?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fsegments_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfcmVmLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/966?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/966?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4c6d357...e7325b2](https://app.codecov.io/gh/boostorg/url/pull/966?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
