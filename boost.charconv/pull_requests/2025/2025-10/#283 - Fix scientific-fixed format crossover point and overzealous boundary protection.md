# #283 Fix scientific/fixed format crossover point and overzealous boundary protection [Merged]

> Username: mborland  
> Created at: 2025-10-23 09:53:35 UTC  
> Updated at: 2025-10-23 16:47:08 UTC  
> Merged at: 2025-10-23 12:15:40 UTC  
> Closed at: 2025-10-23 12:15:40 UTC  
> Url: https://github.com/boostorg/charconv/pull/283  

Closes: #282

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-23 10:47:40 UTC  
> Updated_at: 2025-10-23 16:47:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/283#issuecomment-3436263816  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/283?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 94.92%. Comparing base ([`4a6e906`](https://app.codecov.io/gh/boostorg/charconv/commit/4a6e906388ed84a8f36af24c6d8e751d286c0fa2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`fb9008f`](https://app.codecov.io/gh/boostorg/charconv/commit/fb9008f483c61ed8900d0d1b5fa210fd1282e53c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 7 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/283/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/283?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #283      +/-   ##  
===========================================  
+ Coverage    94.90%   94.92%   +0.02%       
===========================================  
  Files           72       73       +1       
  Lines         9147     9186      +39       
===========================================  
+ Hits          8681     8720      +39       
  Misses         466      466                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/283?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/to\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/283?src=pr&el=tree&filepath=src%2Fto_chars.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzLmNwcA==) | `92.94% <100.00%> (ø)` | |  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/283?src=pr&el=tree&filepath=src%2Fto_chars_float_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `83.45% <100.00%> (+0.05%)` | :arrow_up: |  
| [test/github\_issue\_282.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/283?src=pr&el=tree&filepath=test%2Fgithub_issue_282.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMjgyLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/283?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/283?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4a6e906...fb9008f](https://app.codecov.io/gh/boostorg/charconv/pull/283?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
