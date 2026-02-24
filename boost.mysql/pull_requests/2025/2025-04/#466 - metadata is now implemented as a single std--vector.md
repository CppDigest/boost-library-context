# #466 metadata is now implemented as a single std::vector [Merged]

> Username: anarthal  
> Created at: 2025-04-04 14:37:56 UTC  
> Updated at: 2025-04-05 17:46:55 UTC  
> Merged at: 2025-04-05 17:46:43 UTC  
> Closed at: 2025-04-05 17:46:43 UTC  
> Url: https://github.com/boostorg/mysql/pull/466  

Instead of several separate std::string objects, metadata now uses a single std::vector<char> and offsets, which is more efficient  
Default-constructing metadata objects now leaves all fields in a well-defined state  
Added unit tests for metadata  
  
close #461   
close #465   
close #164

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-04 19:05:22 UTC  
> Updated_at: 2025-04-05 17:46:55 UTC  
> Url: https://github.com/boostorg/mysql/pull/466#issuecomment-2779538882  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/466?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.02%. Comparing base [(`303b9f0`)](https://app.codecov.io/gh/boostorg/mysql/commit/303b9f0b594eac0898079f13f05f48ff3bdc1854?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8f45ade`)](https://app.codecov.io/gh/boostorg/mysql/commit/8f45ade02891b993b78c75cac62f0946ffb46d78?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/466/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/466?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #466   +/-   ##  
========================================  
  Coverage    99.02%   99.02%             
========================================  
  Files          142      142             
  Lines         7262     7280   +18       
========================================  
+ Hits          7191     7209   +18       
  Misses          71       71             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/466?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/metadata.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/466?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fmetadata.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9tZXRhZGF0YS5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/466?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/466?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [303b9f0...8f45ade](https://app.codecov.io/gh/boostorg/mysql/pull/466?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
