# #976 feat: constexpr URL parsing for C++20 [Merged]

> Username: alandefreitas  
> Created at: 2026-02-13 15:44:20 UTC  
> Updated at: 2026-02-20 02:56:13 UTC  
> Merged at: 2026-02-20 02:42:24 UTC  
> Closed at: 2026-02-20 02:42:24 UTC  
> Url: https://github.com/boostorg/url/pull/976  

fix #890

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-02-13 15:50:49 UTC  
> Updated_at: 2026-02-19 22:40:16 UTC  
> Url: https://github.com/boostorg/url/pull/976#issuecomment-3897929993  

An automated preview of the documentation is available at [https://976.url.prtest2.cppalliance.org/index.html](https://976.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-02-19 22:40:15 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-02-13 15:59:56 UTC  
> Updated_at: 2026-02-19 22:47:41 UTC  
> Url: https://github.com/boostorg/url/pull/976#issuecomment-3897970851  

GCOVR code coverage report [https://976.url.prtest2.cppalliance.org/gcovr/index.html](https://976.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://976.url.prtest2.cppalliance.org/genhtml/index.html](https://976.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff Report [https://976.url.prtest2.cppalliance.org/diff-report/index.html](https://976.url.prtest2.cppalliance.org/diff-report/index.html)  
  
Build time: 2026-02-19 22:47:40 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-02-17 02:23:28 UTC  
> Updated_at: 2026-02-20 02:56:13 UTC  
> Url: https://github.com/boostorg/url/pull/976#issuecomment-3911634156  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/976?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `99.31973%` with `6 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 99.12%. Comparing base ([`968127f`](https://app.codecov.io/gh/boostorg/url/commit/968127fba6fe350c213d365f200cad3fe599454f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`0a2c39f`](https://app.codecov.io/gh/boostorg/url/commit/0a2c39f030db375495a82664c05605742e77757e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 4 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/976?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/url/detail/memcpy.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fmemcpy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL21lbWNweS5ocHA=) | 66.66% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/url/impl/params\_encoded\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fimpl%2Fparams_encoded_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZF9yZWYuaHBw) | 97.36% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/url/impl/params\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fimpl%2Fparams_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfcmVmLmhwcA==) | 97.84% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/976/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #976      +/-   ##  
===========================================  
+ Coverage    99.11%   99.12%   +0.01%       
===========================================  
  Files          162      155       -7       
  Lines        10027    10043      +16       
===========================================  
+ Hits          9938     9955      +17       
+ Misses          89       88       -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/976?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [doc/modules/ROOT/examples/unit/snippets.cpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=doc%2Fmodules%2FROOT%2Fexamples%2Funit%2Fsnippets.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZG9jL21vZHVsZXMvUk9PVC9leGFtcGxlcy91bml0L3NuaXBwZXRzLmNwcA==) | `97.31% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/url/authority\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fauthority_view.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/any\_params\_iter.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fany_params_iter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9wYXJhbXNfaXRlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/any\_segments\_iter.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fany_segments_iter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9zZWdtZW50c19pdGVyLmhwcA==) | `98.50% <ø> (ø)` | |  
| [include/boost/url/detail/fnv\_1a.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Ffnv_1a.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2Zudl8xYS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/any\_params\_iter.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fimpl%2Fany_params_iter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3BhcmFtc19pdGVyLmhwcA==) | `99.40% <ø> (ø)` | |  
| [...nclude/boost/url/detail/impl/any\_segments\_iter.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fimpl%2Fany_segments_iter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3NlZ21lbnRzX2l0ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/impl/params\_iter\_impl.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fimpl%2Fparams_iter_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGFyYW1zX2l0ZXJfaW1wbC5ocHA=) | `97.58% <ø> (ø)` | |  
| [...clude/boost/url/detail/impl/segments\_iter\_impl.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fimpl%2Fsegments_iter_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvc2VnbWVudHNfaXRlcl9pbXBsLmhwcA==) | `99.19% <ø> (ø)` | |  
| [include/boost/url/detail/impl/url\_impl.hpp](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fimpl%2Furl_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvdXJsX2ltcGwuaHBw) | `97.40% <100.00%> (ø)` | |  
| ... and [97 more](https://app.codecov.io/gh/boostorg/url/pull/976?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/976/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/976?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/976?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [968127f...0a2c39f](https://app.codecov.io/gh/boostorg/url/pull/976?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
