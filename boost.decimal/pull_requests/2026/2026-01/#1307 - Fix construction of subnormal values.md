# #1307 Fix construction of subnormal values [Merged]

> Username: mborland  
> Created at: 2026-01-21 19:16:42 UTC  
> Updated at: 2026-01-21 23:05:51 UTC  
> Merged at: 2026-01-21 22:37:20 UTC  
> Closed at: 2026-01-21 22:37:20 UTC  
> Url: https://github.com/boostorg/decimal/pull/1307  

Closes: #1306

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-21 22:26:58 UTC  
> Updated_at: 2026-01-21 23:05:51 UTC  
> Url: https://github.com/boostorg/decimal/pull/1307#issuecomment-3781435656  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.9%. Comparing base ([`c46e972`](https://app.codecov.io/gh/cppalliance/decimal/commit/c46e97261ee7232baf8331a60b152eec82e8ef9f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`5d83a8a`](https://app.codecov.io/gh/cppalliance/decimal/commit/5d83a8a7260deaba724d01c57225c826c01a4f7d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 7 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1307/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1307     +/-   ##  
=========================================  
+ Coverage     98.9%   98.9%   +0.1%       
=========================================  
  Files          273     274      +1       
  Lines        17841   17867     +26       
  Branches      1904    1908      +4       
=========================================  
+ Hits         17643   17669     +26       
  Misses         198     198               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/decimal128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwxMjhfdC5ocHA=) | `96.9% <100.0%> (+0.1%)` | :arrow_up: |  
| [include/boost/decimal/decimal32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwzMl90LmhwcA==) | `96.5% <100.0%> (+0.1%)` | :arrow_up: |  
| [include/boost/decimal/decimal64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWw2NF90LmhwcA==) | `95.4% <100.0%> (+0.1%)` | :arrow_up: |  
| [include/boost/decimal/detail/cmath/next.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Fnext.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9uZXh0LmhwcA==) | `95.2% <100.0%> (-2.3%)` | :arrow_down: |  
| [test/github\_issue\_1105.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?src=pr&el=tree&filepath=test%2Fgithub_issue_1105.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTEwNS5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [test/github\_issue\_1306.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?src=pr&el=tree&filepath=test%2Fgithub_issue_1306.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTMwNi5jcHA=) | `100.0% <100.0%> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/decimal/pull/1307/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [c46e972...5d83a8a](https://app.codecov.io/gh/cppalliance/decimal/pull/1307?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
