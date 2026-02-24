# #119 Add 512 bit xoshiro family [Merged]

> Username: mborland  
> Created at: 2025-01-30 19:30:19 UTC  
> Updated at: 2025-01-30 21:14:30 UTC  
> Merged at: 2025-01-30 21:14:21 UTC  
> Closed at: 2025-01-30 21:14:21 UTC  
> Url: https://github.com/boostorg/random/pull/119  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-30 19:52:17 UTC  
> Updated_at: 2025-01-30 21:14:30 UTC  
> Url: https://github.com/boostorg/random/pull/119#issuecomment-2625437466  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/119?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.49%. Comparing base [(`0690197`)](https://app.codecov.io/gh/boostorg/random/commit/0690197c6038b7764187b754a3999f67373ebe86?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2567f7f`)](https://app.codecov.io/gh/boostorg/random/commit/2567f7fc7023d41a98dbef43ba0844f1f6f81881?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/119/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/119?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #119      +/-   ##  
===========================================  
+ Coverage    95.25%   95.49%   +0.24%       
===========================================  
  Files          106      109       +3       
  Lines         7122     7510     +388       
===========================================  
+ Hits          6784     7172     +388       
  Misses         338      338                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/119?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/detail/xoshiro\_base.hpp](https://app.codecov.io/gh/boostorg/random/pull/119?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fdetail%2Fxoshiro_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vZGV0YWlsL3hvc2hpcm9fYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/random/xoshiro.hpp](https://app.codecov.io/gh/boostorg/random/pull/119?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fxoshiro.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20veG9zaGlyby5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_comp\_xoshiro512d.cpp](https://app.codecov.io/gh/boostorg/random/pull/119?src=pr&el=tree&filepath=test%2Ftest_comp_xoshiro512d.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBfeG9zaGlybzUxMmQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_comp\_xoshiro512mm.cpp](https://app.codecov.io/gh/boostorg/random/pull/119?src=pr&el=tree&filepath=test%2Ftest_comp_xoshiro512mm.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBfeG9zaGlybzUxMm1tLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_comp\_xoshiro512pp.cpp](https://app.codecov.io/gh/boostorg/random/pull/119?src=pr&el=tree&filepath=test%2Ftest_comp_xoshiro512pp.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBfeG9zaGlybzUxMnBwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/119?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/119?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0690197...2567f7f](https://app.codecov.io/gh/boostorg/random/pull/119?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
