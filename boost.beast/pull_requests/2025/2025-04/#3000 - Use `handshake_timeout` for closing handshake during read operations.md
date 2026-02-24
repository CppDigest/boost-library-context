# #3000 Use `handshake_timeout` for closing handshake during read operations [Merged]

> Username: ashtum  
> Created at: 2025-04-13 13:01:39 UTC  
> Updated at: 2025-04-26 18:03:42 UTC  
> Merged at: 2025-04-26 17:47:00 UTC  
> Closed at: 2025-04-26 17:47:01 UTC  
> Url: https://github.com/boostorg/beast/pull/3000  

Fixes #2999

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-13 14:51:53 UTC  
> Updated_at: 2025-04-26 18:03:41 UTC  
> Url: https://github.com/boostorg/beast/pull/3000#issuecomment-2799983619  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3000?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.13%. Comparing base [(`1d8dfae`)](https://app.codecov.io/gh/boostorg/beast/commit/1d8dfae490125bd63efedfa7a3ecf20e8e9b54a5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ec20603`)](https://app.codecov.io/gh/boostorg/beast/commit/ec2060303672239f8751f9e6bf05b276567dacc0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3000/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3000?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #3000   +/-   ##  
========================================  
  Coverage    93.13%   93.13%             
========================================  
  Files          177      177             
  Lines        13655    13656    +1       
========================================  
+ Hits         12717    12718    +1       
  Misses         938      938             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/3000?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3000?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fread.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `95.29% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3000?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3000?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1d8dfae...ec20603](https://app.codecov.io/gh/boostorg/beast/pull/3000?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
