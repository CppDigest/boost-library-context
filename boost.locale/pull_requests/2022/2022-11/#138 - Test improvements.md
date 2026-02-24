# #138 Test improvements [Merged]

> Username: Flamefire  
> Created at: 2022-11-21 19:21:38 UTC  
> Updated at: 2022-11-22 08:15:08 UTC  
> Merged at: 2022-11-22 08:15:03 UTC  
> Closed at: 2022-11-22 08:15:04 UTC  
> Url: https://github.com/boostorg/locale/pull/138  

Improve output in case of test failures by calling `TEST` on the input/output operation of the stream and using `TEST_EQ` more often

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-21 21:53:47 UTC  
> Url: https://github.com/boostorg/locale/pull/138#issuecomment-1322699027  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/138?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#138](https://codecov.io/gh/boostorg/locale/pull/138?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (57ca471) into [develop](https://codecov.io/gh/boostorg/locale/commit/7d806962afb86a1013b5291f015d8f06f4757d78?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7d80696) will **decrease** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/138/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/138?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #138      +/-   ##  
===========================================  
- Coverage    88.02%   88.00%   -0.03%       
===========================================  
  Files          104      104                
  Lines         9172     9154      -18       
===========================================  
- Hits          8074     8056      -18       
  Misses        1098     1098                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/138?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/show\_config.cpp](https://codecov.io/gh/boostorg/locale/pull/138/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9zaG93X2NvbmZpZy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/138/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlLmNwcA==) | `94.86% <100.00%> (ø)` | |  
| [test/test\_posix\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/138/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2Zvcm1hdHRpbmcuY3Bw) | `95.45% <100.00%> (-0.30%)` | :arrow_down: |  
| [test/test\_std\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/138/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9mb3JtYXR0aW5nLmNwcA==) | `96.00% <100.00%> (-0.13%)` | :arrow_down: |  
| [test/test\_winapi\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/138/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9mb3JtYXR0aW5nLmNwcA==) | `95.12% <100.00%> (-0.29%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/138?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/138?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7d80696...57ca471](https://codecov.io/gh/boostorg/locale/pull/138?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
