# #288 Fix types for compatibility with int128 [Merged]

> Username: mborland  
> Created at: 2026-01-27 19:46:35 UTC  
> Updated at: 2026-01-28 13:04:03 UTC  
> Merged at: 2026-01-28 13:03:21 UTC  
> Closed at: 2026-01-28 13:03:21 UTC  
> Url: https://github.com/boostorg/charconv/pull/288  

See: https://github.com/cppalliance/int128

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-27 23:17:01 UTC  
> Updated_at: 2026-01-28 13:04:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/288#issuecomment-3808054349  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/288?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 94.84%. Comparing base ([`bb1a428`](https://app.codecov.io/gh/boostorg/charconv/commit/bb1a4284c0859f85b2a40b57326ebf6f4592c936?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`c5ede94`](https://app.codecov.io/gh/boostorg/charconv/commit/c5ede940e29a254f62da7f67c68754c0ab495ca7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/288/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/288?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #288   +/-   ##  
========================================  
  Coverage    94.84%   94.84%             
========================================  
  Files           73       73             
  Lines         8986     8986             
========================================  
  Hits          8523     8523             
  Misses         463      463             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/288?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/charconv/detail/integer\_search\_trees.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/288?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Finteger_search_trees.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvaW50ZWdlcl9zZWFyY2hfdHJlZXMuaHBw) | `99.25% <100.00%> (ø)` | |  
| [...de/boost/charconv/detail/to\_chars\_integer\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/288?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fto_chars_integer_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvdG9fY2hhcnNfaW50ZWdlcl9pbXBsLmhwcA==) | `96.89% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/288?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/288?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bb1a428...c5ede94](https://app.codecov.io/gh/boostorg/charconv/pull/288?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
