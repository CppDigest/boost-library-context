# #118 Add 256 bit xoshiro family [Merged]

> Username: mborland  
> Created at: 2025-01-29 22:14:53 UTC  
> Updated at: 2025-01-30 19:29:40 UTC  
> Merged at: 2025-01-30 19:29:28 UTC  
> Closed at: 2025-01-30 19:29:28 UTC  
> Url: https://github.com/boostorg/random/pull/118  

The base class should allow us to readily implement the 128, 512, and 1024 bit versions should people want them.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-29 23:16:55 UTC  
> Updated_at: 2025-01-30 19:29:40 UTC  
> Url: https://github.com/boostorg/random/pull/118#issuecomment-2623140088  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/118?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.25%. Comparing base [(`13bbc0e`)](https://app.codecov.io/gh/boostorg/random/commit/13bbc0ec7e99e9c1a6d9373be925118b99055865?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`fa07586`)](https://app.codecov.io/gh/boostorg/random/commit/fa075862add5fa6c512ab86af8ca8a51e5a03e1f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 33 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/118/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/118?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #118      +/-   ##  
===========================================  
+ Coverage    94.91%   95.25%   +0.34%       
===========================================  
  Files          101      106       +5       
  Lines         6644     7122     +478       
===========================================  
+ Hits          6306     6784     +478       
  Misses         338      338                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/118?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/detail/xoshiro\_base.hpp](https://app.codecov.io/gh/boostorg/random/pull/118?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fdetail%2Fxoshiro_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vZGV0YWlsL3hvc2hpcm9fYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/random/xoshiro.hpp](https://app.codecov.io/gh/boostorg/random/pull/118?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fxoshiro.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20veG9zaGlyby5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_comp\_xoshiro256d.cpp](https://app.codecov.io/gh/boostorg/random/pull/118?src=pr&el=tree&filepath=test%2Ftest_comp_xoshiro256d.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBfeG9zaGlybzI1NmQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_comp\_xoshiro256mm.cpp](https://app.codecov.io/gh/boostorg/random/pull/118?src=pr&el=tree&filepath=test%2Ftest_comp_xoshiro256mm.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBfeG9zaGlybzI1Nm1tLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_comp\_xoshiro256pp.cpp](https://app.codecov.io/gh/boostorg/random/pull/118?src=pr&el=tree&filepath=test%2Ftest_comp_xoshiro256pp.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBfeG9zaGlybzI1NnBwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_generator.ipp](https://app.codecov.io/gh/boostorg/random/pull/118?src=pr&el=tree&filepath=test%2Ftest_generator.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5pcHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/118?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/118?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [13bbc0e...fa07586](https://app.codecov.io/gh/boostorg/random/pull/118?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
