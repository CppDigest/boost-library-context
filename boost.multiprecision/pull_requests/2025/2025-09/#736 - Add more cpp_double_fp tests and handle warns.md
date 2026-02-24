# #736 Add more cpp_double_fp tests and handle warns [Merged]

> Username: ckormanyos  
> Created at: 2025-09-07 17:40:52 UTC  
> Updated at: 2025-09-08 15:07:00 UTC  
> Merged at: 2025-09-08 09:29:42 UTC  
> Closed at: 2025-09-08 09:29:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/736  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-09-07 17:46:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/736#issuecomment-3263934516  

The purpose of this PR is to handle warnings in `cpp_double_fp_backend` and add a few more tests.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-09-08 07:52:06 UTC  
> Updated_at: 2025-09-08 15:07:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/736#issuecomment-3265039939  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/736?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 96.2%. Comparing base ([`2135e30`](https://app.codecov.io/gh/boostorg/multiprecision/commit/2135e306f693cfb3ecb5d07472b13663d235bf39?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`50f5c67`](https://app.codecov.io/gh/boostorg/multiprecision/commit/50f5c670539dff8bfa302cf1cd44a2fe55fe5f96?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 8 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/736/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/736?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #736     +/-   ##  
=========================================  
+ Coverage     96.2%   96.2%   +0.1%       
=========================================  
  Files          298     300      +2       
  Lines        31242   31349    +107       
=========================================  
+ Hits         30031   30141    +110       
+ Misses        1211    1208      -3       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/736?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/736?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `100.0% <ø> (ø)` | |  
| [...est\_cpp\_double\_float\_bessel\_versus\_bin\_and\_dec.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/736?src=pr&el=tree&filepath=test%2Ftest_cpp_double_float_bessel_versus_bin_and_dec.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NwcF9kb3VibGVfZmxvYXRfYmVzc2VsX3ZlcnN1c19iaW5fYW5kX2RlYy5jcHA=) | `100.0% <100.0%> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/736/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/736?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/736?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2135e30...50f5c67](https://app.codecov.io/gh/boostorg/multiprecision/pull/736?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
