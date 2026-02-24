# #968 docs: align README/Antora examples [Merged]

> Username: alandefreitas  
> Created at: 2026-01-26 23:34:20 UTC  
> Updated at: 2026-01-27 16:56:34 UTC  
> Merged at: 2026-01-27 16:56:01 UTC  
> Closed at: 2026-01-27 16:56:01 UTC  
> Url: https://github.com/boostorg/url/pull/968  

The transition from quickbook to antora created two sources of truth for testing code snippets. We fix this and include missing snippets in the tests.   
  
fix #967

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-26 23:41:32 UTC  
> Updated_at: 2026-01-27 05:29:51 UTC  
> Url: https://github.com/boostorg/url/pull/968#issuecomment-3802296800  

An automated preview of the documentation is available at [https://968.url.prtest2.cppalliance.org/index.html](https://968.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-27 05:29:50 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-01-26 23:52:17 UTC  
> Updated_at: 2026-01-27 15:47:58 UTC  
> Url: https://github.com/boostorg/url/pull/968#issuecomment-3802330392  

GCOVR code coverage report [https://968.url.prtest2.cppalliance.org/gcovr/index.html](https://968.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://968.url.prtest2.cppalliance.org/genhtml/index.html](https://968.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff Report [https://968.url.prtest2.cppalliance.org/diff-report/index.html](https://968.url.prtest2.cppalliance.org/diff-report/index.html)  
  
Build time: 2026-01-27 15:47:57 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-01-27 05:52:19 UTC  
> Updated_at: 2026-01-27 16:56:34 UTC  
> Url: https://github.com/boostorg/url/pull/968#issuecomment-3803255330  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/968?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `98.42105%` with `6 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 99.14%. Comparing base ([`14fb347`](https://app.codecov.io/gh/boostorg/url/commit/14fb3478961ccf9d4fc44c6b26797934a44f8c89?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`25bd3ce`](https://app.codecov.io/gh/boostorg/url/commit/25bd3cef65d79cb52e3a7876707038291030f2c9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/968?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [doc/modules/ROOT/examples/unit/snippets.cpp](https://app.codecov.io/gh/boostorg/url/pull/968?src=pr&el=tree&filepath=doc%2Fmodules%2FROOT%2Fexamples%2Funit%2Fsnippets.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZG9jL21vZHVsZXMvUk9PVC9leGFtcGxlcy91bml0L3NuaXBwZXRzLmNwcA==) | 98.42% | [6 Missing :warning: ](https://app.codecov.io/gh/boostorg/url/pull/968?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/968/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/968?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #968      +/-   ##  
===========================================  
- Coverage    99.35%   99.14%   -0.21%       
===========================================  
  Files          161      162       +1       
  Lines         8771     9937    +1166       
===========================================  
+ Hits          8714     9852    +1138       
- Misses          57       85      +28       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/968?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/format.hpp](https://app.codecov.io/gh/boostorg/url/pull/968?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fformat.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZm9ybWF0LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/968?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Furl_view_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [doc/modules/ROOT/examples/unit/snippets.cpp](https://app.codecov.io/gh/boostorg/url/pull/968?src=pr&el=tree&filepath=doc%2Fmodules%2FROOT%2Fexamples%2Funit%2Fsnippets.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZG9jL21vZHVsZXMvUk9PVC9leGFtcGxlcy91bml0L3NuaXBwZXRzLmNwcA==) | `97.25% <98.42%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/968/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/968?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/968?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [14fb347...25bd3ce](https://app.codecov.io/gh/boostorg/url/pull/968?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
