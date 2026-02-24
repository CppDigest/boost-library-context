# #683 A few header-only warns and warns in CI [Merged]

> Username: ckormanyos  
> Created at: 2025-04-26 12:22:15 UTC  
> Updated at: 2025-04-26 17:30:50 UTC  
> Merged at: 2025-04-26 12:58:14 UTC  
> Closed at: 2025-04-26 12:58:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/683  

In this PR, we have found a few remaining warnings `-Wconversion` and `-Wsign-conversion` in the code and cleaned these up.  
  
There was also a deprecated test header being used in the setup of the specfun tests. This has been corrected and the output logs of the specfun tests are expected to be much cleaner.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-26 12:55:56 UTC  
> Updated_at: 2025-04-26 13:19:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/683#issuecomment-2832140569  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/683?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`d884d70`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/d884d70d17a8db933d92f37b0c8513b2b5b4e8e9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e1cc7ba`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/e1cc7ba66d72a444458cdce5d3335fec9518fd5c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/683/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/683?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #683   +/-   ##  
=======================================  
  Coverage     94.1%   94.1%             
=======================================  
  Files          279     279             
  Lines        28983   28983             
=======================================  
  Hits         27257   27257             
  Misses        1726    1726             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/683?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_bin\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/683?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_bin_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfYmluX2Zsb2F0LmhwcA==) | `89.7% <100.0%> (ø)` | |  
| [include/boost/multiprecision/cpp\_dec\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/683?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_dec_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGVjX2Zsb2F0LmhwcA==) | `94.0% <100.0%> (ø)` | |  
| [include/boost/multiprecision/cpp\_int/bitwise.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/683?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fbitwise.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2JpdHdpc2UuaHBw) | `85.2% <100.0%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/683?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/683?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d884d70...e1cc7ba](https://app.codecov.io/gh/boostorg/multiprecision/pull/683?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-04-26 17:30:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/683#issuecomment-2832458651  

Thanks Chris!

---
