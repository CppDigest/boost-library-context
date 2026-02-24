# #978 Fix minor parsing and iteration bugs [Merged]

> Username: alandefreitas  
> Created at: 2026-02-20 17:19:09 UTC  
> Updated at: 2026-02-20 23:42:33 UTC  
> Merged at: 2026-02-20 23:41:29 UTC  
> Closed at: 2026-02-20 23:41:29 UTC  
> Url: https://github.com/boostorg/url/pull/978  

- Fix `params_iter_impl::decrement()` computing incorrect decoded  
key/value sizes when a query parameter's value contains literal `=`  
characters (e.g. `?a=b=c`), causing out-of-bounds reads during  
backward iteration (#972)  
- Fix `decode_view::remove_prefix`/`remove_suffix` asserting `n <=  
size()` instead of silently allowing undefined behavior (#973)  
- Add test for URL with `?` in query string (#926)

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-02-20 17:25:21 UTC  
> Url: https://github.com/boostorg/url/pull/978#issuecomment-3936183057  

An automated preview of the documentation is available at [https://978.url.prtest2.cppalliance.org/index.html](https://978.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-02-20 17:25:20 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-02-20 17:32:30 UTC  
> Url: https://github.com/boostorg/url/pull/978#issuecomment-3936212588  

GCOVR code coverage report [https://978.url.prtest2.cppalliance.org/gcovr/index.html](https://978.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://978.url.prtest2.cppalliance.org/genhtml/index.html](https://978.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff Report [https://978.url.prtest2.cppalliance.org/diff-report/index.html](https://978.url.prtest2.cppalliance.org/diff-report/index.html)  
  
Build time: 2026-02-20 17:32:28 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-02-20 17:48:22 UTC  
> Updated_at: 2026-02-20 23:42:33 UTC  
> Url: https://github.com/boostorg/url/pull/978#issuecomment-3936282973  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/978?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.15%. Comparing base ([`062fe00`](https://app.codecov.io/gh/boostorg/url/commit/062fe009bf5623ccdf9a56c6ba2baae31b5e6e62?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`14e21e2`](https://app.codecov.io/gh/boostorg/url/commit/14e21e2a10d2aca61e39853d498783895a266c58?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/978/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/978?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #978      +/-   ##  
===========================================  
+ Coverage    99.12%   99.15%   +0.03%       
===========================================  
  Files          155      155                
  Lines        10043    10045       +2       
===========================================  
+ Hits          9955     9960       +5       
+ Misses          88       85       -3       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/978?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/decode\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/978?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdecode_view.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/impl/params\_iter\_impl.hpp](https://app.codecov.io/gh/boostorg/url/pull/978?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fimpl%2Fparams_iter_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGFyYW1zX2l0ZXJfaW1wbC5ocHA=) | `100.00% <100.00%> (+2.41%)` | :arrow_up: |  
| [src/decode\_view.cpp](https://app.codecov.io/gh/boostorg/url/pull/978?src=pr&el=tree&filepath=src%2Fdecode_view.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RlY29kZV92aWV3LmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/978?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/978?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [062fe00...14e21e2](https://app.codecov.io/gh/boostorg/url/pull/978?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
