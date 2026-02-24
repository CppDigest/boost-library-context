# #120 Add the 128 bit xoshiro PRNGs [Merged]

> Username: mborland  
> Created at: 2025-01-30 21:22:03 UTC  
> Updated at: 2025-01-31 00:40:38 UTC  
> Merged at: 2025-01-31 00:40:26 UTC  
> Closed at: 2025-01-31 00:40:26 UTC  
> Url: https://github.com/boostorg/random/pull/120  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-30 21:33:16 UTC  
> Updated_at: 2025-01-31 00:40:38 UTC  
> Url: https://github.com/boostorg/random/pull/120#issuecomment-2625641726  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/120?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.74%. Comparing base [(`c33cbae`)](https://app.codecov.io/gh/boostorg/random/commit/c33cbaeb31201926b70b04bc4342349f2414be6f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`0d4c9f4`)](https://app.codecov.io/gh/boostorg/random/commit/0d4c9f403cf7e5e57c8ed5d0963ea1c179ae26ff?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 10 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/120/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #120      +/-   ##  
===========================================  
+ Coverage    95.49%   95.74%   +0.24%       
===========================================  
  Files          109      112       +3       
  Lines         7510     7941     +431       
===========================================  
+ Hits          7172     7603     +431       
  Misses         338      338                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/120?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/detail/xoshiro\_base.hpp](https://app.codecov.io/gh/boostorg/random/pull/120?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fdetail%2Fxoshiro_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vZGV0YWlsL3hvc2hpcm9fYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/random/xoshiro.hpp](https://app.codecov.io/gh/boostorg/random/pull/120?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fxoshiro.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20veG9zaGlyby5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_comp\_xoshiro128f.cpp](https://app.codecov.io/gh/boostorg/random/pull/120?src=pr&el=tree&filepath=test%2Ftest_comp_xoshiro128f.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBfeG9zaGlybzEyOGYuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_comp\_xoshiro128mm.cpp](https://app.codecov.io/gh/boostorg/random/pull/120?src=pr&el=tree&filepath=test%2Ftest_comp_xoshiro128mm.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBfeG9zaGlybzEyOG1tLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_comp\_xoshiro128pp.cpp](https://app.codecov.io/gh/boostorg/random/pull/120?src=pr&el=tree&filepath=test%2Ftest_comp_xoshiro128pp.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBfeG9zaGlybzEyOHBwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/120?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/120?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c33cbae...0d4c9f4](https://app.codecov.io/gh/boostorg/random/pull/120?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
