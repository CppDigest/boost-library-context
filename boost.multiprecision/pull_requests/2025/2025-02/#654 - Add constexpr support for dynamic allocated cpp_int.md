# #654 Add constexpr support for dynamic allocated cpp_int [Open]

> Username: marcoffee  
> Created at: 2025-02-13 23:15:09 UTC  
> Updated at: 2025-07-12 11:52:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/654  

Added constexpr support for dynamic allocated `cpp_int`s as long as compiler implements features `__cpp_constexpr_dynamic_alloc` and `__cpp_lib_constexpr_dynamic_alloc` from [P0784R7](https://wg21.link/P0784R7).  
  
If those features are disabled, it behaves as the current implementation. Note that those features require C++20, so they will only work when passing `-std=c++2a` (or beyond) flag during compilation through a supported compiler (tested here on `g++-11`+ and `clang++-16`+ using libstdc++ with `g++-10`+ toolset on ubuntu noble).  
  
It might work on `g++-10`, but for some reason the b2 check for `if constexpr` keep returning "no" for it.  
  
Also fixed constexpr construction from strings and added some tests for construction/operations on dynamic allocated `cpp_int`s.  
  
Had to make small changes to existing code to tackle some warnings given when compiled without the feature's support (such as uninitialized variables not being supported in constexpr methods before C++20).

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-03-24 19:24:51 UTC  
> Updated_at: 2025-07-12 11:52:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/654#issuecomment-2749183793  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `89.72603%` with `15 lines` in your changes missing coverage. Please review.  
> Project coverage is 94.1%. Comparing base [(`7fac807`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/7fac807c4624fb27e8b2f75433028d926b8ba3f4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2c0d0cd`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/2c0d0cdc00d1038ff26764acde1c6205a1972064?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 847 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50LmhwcA==) | 88.2% | [15 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/654/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #654     +/-   ##  
=========================================  
- Coverage     94.1%   94.1%   -0.0%       
=========================================  
  Files          279     282      +3       
  Lines        28979   29022     +43       
=========================================  
+ Hits         27253   27288     +35       
- Misses        1726    1734      +8       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/multiprecision/cpp\_int/cpp\_int\_config.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fcpp_int_config.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2NwcF9pbnRfY29uZmlnLmhwcA==) | `100.0% <ø> (ø)` | |  
| [include/boost/multiprecision/cpp\_int/limits.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Flimits.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2xpbWl0cy5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [...nclude/boost/multiprecision/detail/empty\_value.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Fempty_value.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZW1wdHlfdmFsdWUuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...nclude/boost/multiprecision/detail/number\_base.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Fnumber_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvbnVtYmVyX2Jhc2UuaHBw) | `98.0% <ø> (ø)` | |  
| [test/constexpr\_test\_dynamic\_cpp\_int.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&filepath=test%2Fconstexpr_test_dynamic_cpp_int.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jb25zdGV4cHJfdGVzdF9keW5hbWljX2NwcF9pbnQuaHBw) | `100.0% <100.0%> (ø)` | |  
| [test/constexpr\_test\_dynamic\_cpp\_int\_1.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&filepath=test%2Fconstexpr_test_dynamic_cpp_int_1.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jb25zdGV4cHJfdGVzdF9keW5hbWljX2NwcF9pbnRfMS5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [test/constexpr\_test\_dynamic\_cpp\_int\_2.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&filepath=test%2Fconstexpr_test_dynamic_cpp_int_2.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jb25zdGV4cHJfdGVzdF9keW5hbWljX2NwcF9pbnRfMi5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [include/boost/multiprecision/cpp\_int.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50LmhwcA==) | `95.1% <88.2%> (-0.6%)` | :arrow_down: |  
  
... and [5 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/654/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7fac807...2c0d0cd](https://app.codecov.io/gh/boostorg/multiprecision/pull/654?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
