# #739 Simplify syntax cpp_double_fp_backend [Merged]

> Username: ckormanyos  
> Created at: 2025-09-20 06:40:44 UTC  
> Updated at: 2025-09-20 17:47:06 UTC  
> Merged at: 2025-09-20 12:52:22 UTC  
> Closed at: 2025-09-20 12:52:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/739  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-20 07:44:28 UTC  
> Updated_at: 2025-09-20 17:47:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/739#issuecomment-3314743682  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 96.2%. Comparing base ([`6ec5a7d`](https://app.codecov.io/gh/boostorg/multiprecision/commit/6ec5a7dd9da9c5151192b5cc87b01ce28d6a2545?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`156e03e`](https://app.codecov.io/gh/boostorg/multiprecision/commit/156e03e11e076b14432710f16f79d1e8183e17c6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/739/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #739     +/-   ##  
=========================================  
- Coverage     96.2%   96.2%   -0.0%       
=========================================  
  Files          300     300               
  Lines        31349   31384     +35       
=========================================  
+ Hits         30141   30174     +33       
- Misses        1208    1210      +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...precision/cpp\_df\_qf/cpp\_df\_qf\_detail\_constants.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_constants.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jb25zdGFudHMuaHBw) | `100.0% <ø> (ø)` | |  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [...est\_cpp\_double\_float\_bessel\_versus\_bin\_and\_dec.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?src=pr&el=tree&filepath=test%2Ftest_cpp_double_float_bessel_versus_bin_and_dec.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NwcF9kb3VibGVfZmxvYXRfYmVzc2VsX3ZlcnN1c19iaW5fYW5kX2RlYy5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [test/test\_various\_edges\_more.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?src=pr&el=tree&filepath=test%2Ftest_various_edges_more.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3ZhcmlvdXNfZWRnZXNfbW9yZS5jcHA=) | `100.0% <100.0%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/739/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6ec5a7d...156e03e](https://app.codecov.io/gh/boostorg/multiprecision/pull/739?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
