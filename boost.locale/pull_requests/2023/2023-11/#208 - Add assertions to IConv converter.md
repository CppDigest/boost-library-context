# #208 Add assertions to IConv converter [Merged]

> Username: Flamefire  
> Created at: 2023-11-28 10:28:10 UTC  
> Updated at: 2023-11-28 19:19:23 UTC  
> Merged at: 2023-11-28 19:19:20 UTC  
> Closed at: 2023-11-28 19:19:20 UTC  
> Url: https://github.com/boostorg/locale/pull/208  

Assert post-conditions of calling `iconv` and add test for a possible violation of that.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-11-28 17:06:25 UTC  
> Url: https://github.com/boostorg/locale/pull/208#issuecomment-1830312075  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/208?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#208](https://app.codecov.io/gh/boostorg/locale/pull/208?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1de61bf) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/8445dce654c400b724e4cb8391b82e8e742eeca8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8445dce) will **increase** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/208/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/208?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #208      +/-   ##  
===========================================  
+ Coverage    95.63%   95.65%   +0.02%       
===========================================  
  Files          114      114                
  Lines        10138    10145       +7       
===========================================  
+ Hits          9695     9704       +9       
+ Misses         443      441       -2       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/locale/pull/208?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/iconv\_converter.hpp](https://app.codecov.io/gh/boostorg/locale/pull/208?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9pY29udl9jb252ZXJ0ZXIuaHBw) | `94.64% <100.00%> (+0.30%)` | :arrow_up: |  
| [test/test\_encoding.cpp](https://app.codecov.io/gh/boostorg/locale/pull/208?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VuY29kaW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/208/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/208?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/208?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8445dce...1de61bf](https://app.codecov.io/gh/boostorg/locale/pull/208?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
