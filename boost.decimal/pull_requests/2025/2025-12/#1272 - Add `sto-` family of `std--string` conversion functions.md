# #1272 Add `sto*` family of `std::string` conversion functions [Merged]

> Username: mborland  
> Created at: 2025-12-05 13:55:12 UTC  
> Updated at: 2025-12-08 16:38:26 UTC  
> Merged at: 2025-12-08 08:10:19 UTC  
> Closed at: 2025-12-08 08:10:19 UTC  
> Url: https://github.com/boostorg/decimal/pull/1272  

Closes: #1270

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-12-05 13:57:51 UTC  
> Url: https://github.com/boostorg/decimal/pull/1272#issuecomment-3617035672  

An automated preview of the documentation is available at [https://1272.decimal.prtest3.cppalliance.org/libs/decimal/doc/html/index.html](https://1272.decimal.prtest3.cppalliance.org/libs/decimal/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-12-05 13:57:50 UTC

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-12-05 16:55:59 UTC  
> Updated_at: 2025-12-08 16:38:26 UTC  
> Url: https://github.com/boostorg/decimal/pull/1272#issuecomment-3617714061  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1272?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.9%. Comparing base ([`2bc8e5f`](https://app.codecov.io/gh/cppalliance/decimal/commit/2bc8e5fbbbb6d2f468a87380ea8853026e7d20e3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`0d0bd12`](https://app.codecov.io/gh/cppalliance/decimal/commit/0d0bd12fe877d5170387f2df2f5db75810cda2ac?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 25 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1272/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1272?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1272     +/-   ##  
=========================================  
+ Coverage     98.9%   98.9%   +0.1%       
=========================================  
  Files          266     267      +1       
  Lines        17698   17755     +57       
  Branches      1970    1973      +3       
=========================================  
+ Hits         17492   17550     +58       
+ Misses         206     205      -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1272?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/string.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1272?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fstring.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL3N0cmluZy5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [test/test\_from\_string.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1272?src=pr&el=tree&filepath=test%2Ftest_from_string.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zyb21fc3RyaW5nLmNwcA==) | `100.0% <100.0%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/decimal/pull/1272/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1272?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1272?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [2bc8e5f...0d0bd12](https://app.codecov.io/gh/cppalliance/decimal/pull/1272?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
