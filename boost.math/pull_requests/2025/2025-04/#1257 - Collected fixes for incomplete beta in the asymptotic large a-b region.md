# #1257 Collected fixes for incomplete beta in the asymptotic large a/b region [Merged]

> Username: jzmaddock  
> Created at: 2025-04-13 09:14:13 UTC  
> Updated at: 2025-04-15 19:11:58 UTC  
> Merged at: 2025-04-14 11:17:54 UTC  
> Closed at: 2025-04-14 11:17:54 UTC  
> Url: https://github.com/boostorg/math/pull/1257  

Fixes #1198.  
  
This change introduces some rather extreme new test cases, expect CI carnage!

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-14 09:53:32 UTC  
> Updated_at: 2025-04-14 11:18:28 UTC  
> Url: https://github.com/boostorg/math/pull/1257#issuecomment-2801140751  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1257?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.84%. Comparing base [(`e818106`)](https://app.codecov.io/gh/boostorg/math/commit/e8181069f836664fb57c288f1e367dbbbd967f9b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e8c86a8`)](https://app.codecov.io/gh/boostorg/math/commit/e8c86a8f12a6e90f820dd786f7f2d16195ff5f99?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 17 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1257/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1257?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1257   +/-   ##  
========================================  
  Coverage    93.83%   93.84%             
========================================  
  Files          657      658    +1       
  Lines        55244    55295   +51       
========================================  
+ Hits         51840    51891   +51       
  Misses        3404     3404             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1257?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/math/distributions/non\_central\_beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1257?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_beta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfYmV0YS5ocHA=) | `92.09% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1257?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_ik.hpp](https://app.codecov.io/gh/boostorg/math/pull/1257?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_ik.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfaWsuaHBw) | `99.44% <100.00%> (ø)` | |  
| [test/test\_ibeta.cpp](https://app.codecov.io/gh/boostorg/math/pull/1257?src=pr&el=tree&filepath=test%2Ftest_ibeta.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2liZXRhLmNwcA==) | `97.91% <100.00%> (+0.41%)` | :arrow_up: |  
| [test/test\_ibeta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1257?src=pr&el=tree&filepath=test%2Ftest_ibeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2liZXRhLmhwcA==) | `99.03% <100.00%> (+0.01%)` | :arrow_up: |  
| [test/test\_nc\_f.cpp](https://app.codecov.io/gh/boostorg/math/pull/1257?src=pr&el=tree&filepath=test%2Ftest_nc_f.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X25jX2YuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1257/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1257?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1257?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e818106...e8c86a8](https://app.codecov.io/gh/boostorg/math/pull/1257?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-04-14 11:17:47 UTC  
> Url: https://github.com/boostorg/math/pull/1257#issuecomment-2801371968  

Just the sycl tests failing so merging...

---

## Comment 3

> Username: dschmitz89  
> Created_at: 2025-04-14 11:44:56 UTC  
> Url: https://github.com/boostorg/math/pull/1257#issuecomment-2801438995  

Thanks @jzmaddock ! Do you expect this to also close https://github.com/scipy/scipy/issues/21725 ? Another case of large a and b.

---

## Comment 4

> Username: dschmitz89  
> Created_at: 2025-04-15 19:11:57 UTC  
> Url: https://github.com/boostorg/math/pull/1257#issuecomment-2807225819  

> Thanks @jzmaddock ! Do you expect this to also close [scipy/scipy#21725](https://github.com/scipy/scipy/issues/21725) ? Another case of large a and b.  
  
Looks like it does not.

---
