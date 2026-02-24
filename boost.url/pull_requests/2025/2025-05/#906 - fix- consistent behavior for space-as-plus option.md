# #906 fix: consistent behavior for space-as-plus option [Merged]

> Username: alandefreitas  
> Created at: 2025-05-14 20:31:12 UTC  
> Updated at: 2025-05-15 21:04:19 UTC  
> Merged at: 2025-05-15 21:04:06 UTC  
> Closed at: 2025-05-15 21:04:06 UTC  
> Url: https://github.com/boostorg/url/pull/906  

This commit refactors all functions so they have consistent behavior for the space-as-plus encoding option.  
  
- any_params_iter objects store and apply the appropriate option when measuring and copying  
- when the option is enabled, encoding functions encode space-as-plus and plus as %2B regardless of the charset  
- normalization and comparison algorithms take into consideration special query chars whose meaning changes depending on encoding  
- all params_view objects created with default options enable space-as-plus encoding  
  
fix #903

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-05-14 20:36:48 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2881500350  

An automated preview of the documentation is available at [https://906.url.prtest2.cppalliance.org/index.html](https://906.url.prtest2.cppalliance.org/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-05-14 20:49:42 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2881542394  

GCOVR code coverage report [https://906.url.prtest2.cppalliance.org/gcovr/index.html](https://906.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://906.url.prtest2.cppalliance.org/genhtml/index.html](https://906.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-05-14 21:11:23 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2881590316  

An automated preview of the documentation is available at [https://906.url.prtest2.cppalliance.org/index.html](https://906.url.prtest2.cppalliance.org/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-05-14 21:25:01 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2881618034  

GCOVR code coverage report [https://906.url.prtest2.cppalliance.org/gcovr/index.html](https://906.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://906.url.prtest2.cppalliance.org/genhtml/index.html](https://906.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-05-14 21:46:23 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2881659323  

An automated preview of the documentation is available at [https://906.url.prtest2.cppalliance.org/index.html](https://906.url.prtest2.cppalliance.org/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-05-14 22:00:45 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2881686056  

GCOVR code coverage report [https://906.url.prtest2.cppalliance.org/gcovr/index.html](https://906.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://906.url.prtest2.cppalliance.org/genhtml/index.html](https://906.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-05-14 22:36:21 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2881746823  

An automated preview of the documentation is available at [https://906.url.prtest2.cppalliance.org/index.html](https://906.url.prtest2.cppalliance.org/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2025-05-14 22:49:31 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2881768167  

GCOVR code coverage report [https://906.url.prtest2.cppalliance.org/gcovr/index.html](https://906.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://906.url.prtest2.cppalliance.org/genhtml/index.html](https://906.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2025-05-14 22:56:29 UTC  
> Updated_at: 2025-05-15 21:04:17 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2881777159  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/906?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.21%. Comparing base [(`c5d11a9`)](https://app.codecov.io/gh/boostorg/url/commit/c5d11a9c0e52d195f9b8cf234684371c489c4cc6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3b7c26d`)](https://app.codecov.io/gh/boostorg/url/commit/3b7c26dabb62c3af2a697ca8893f05d388faa894?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/906/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #906   +/-   ##  
========================================  
  Coverage    99.20%   99.21%             
========================================  
  Files          157      158    +1       
  Lines         8436     8493   +57       
========================================  
+ Hits          8369     8426   +57       
  Misses          67       67             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/906?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/any\_params\_iter.hpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fany_params_iter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9wYXJhbXNfaXRlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/url\_impl.hpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Furl_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3VybF9pbXBsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/encoding\_opts.hpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fencoding_opts.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZW5jb2Rpbmdfb3B0cy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/encode.hpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fimpl%2Fencode.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9lbmNvZGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/params\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fimpl%2Fparams_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfcmVmLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/param.hpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fparam.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW0uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Furl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [src/detail/any\_params\_iter.cpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=src%2Fdetail%2Fany_params_iter.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9hbnlfcGFyYW1zX2l0ZXIuY3Bw) | `99.40% <100.00%> (+<0.01%)` | :arrow_up: |  
| [src/detail/normalize.cpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=src%2Fdetail%2Fnormalize.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9ub3JtYWxpemUuY3Bw) | `99.30% <100.00%> (+0.05%)` | :arrow_up: |  
| [src/detail/normalize.hpp](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree&filepath=src%2Fdetail%2Fnormalize.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9ub3JtYWxpemUuaHBw) | `100.00% <ø> (ø)` | |  
| ... and [7 more](https://app.codecov.io/gh/boostorg/url/pull/906?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/906/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/906?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/906?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c5d11a9...3b7c26d](https://app.codecov.io/gh/boostorg/url/pull/906?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2025-05-15 19:46:28 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2884894303  

An automated preview of the documentation is available at [https://906.url.prtest2.cppalliance.org/index.html](https://906.url.prtest2.cppalliance.org/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2025-05-15 19:55:29 UTC  
> Url: https://github.com/boostorg/url/pull/906#issuecomment-2884912905  

GCOVR code coverage report [https://906.url.prtest2.cppalliance.org/gcovr/index.html](https://906.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://906.url.prtest2.cppalliance.org/genhtml/index.html](https://906.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://906.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
