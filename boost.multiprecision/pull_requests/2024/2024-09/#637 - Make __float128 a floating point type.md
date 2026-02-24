# #637 Make __float128 a floating point type. [Merged]

> Username: jzmaddock  
> Created at: 2024-09-07 18:10:44 UTC  
> Updated at: 2024-09-22 12:16:34 UTC  
> Merged at: 2024-09-22 12:16:21 UTC  
> Closed at: 2024-09-22 12:16:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/637  

Allows float128 to be used in non gnu mode.  
Enable some tests for non-gnu mode.  
Fixes https://github.com/boostorg/multiprecision/issues/569.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-22 09:35:13 UTC  
> Updated_at: 2024-09-22 12:16:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/637#issuecomment-2366367487  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/637?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.0%. Comparing base [(`0ab75f9`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/0ab75f95ab360b792fa9bdb7a5a9a573ebfe3747?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9ba6ffd`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/9ba6ffd9f7e4c913383583e4c3223acbfd521b42?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/637/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/637?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #637     +/-   ##  
=========================================  
+ Coverage     92.5%   94.0%   +1.6%       
=========================================  
  Files          254     279     +25       
  Lines        23475   27033   +3558       
=========================================  
+ Hits         21699   25396   +3697       
+ Misses        1776    1637    -139       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/637?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [config/has\_basic\_float128.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/637?src=pr&el=tree&filepath=config%2Fhas_basic_float128.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-Y29uZmlnL2hhc19iYXNpY19mbG9hdDEyOC5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [...nclude/boost/multiprecision/detail/number\_base.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/637?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Fnumber_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvbnVtYmVyX2Jhc2UuaHBw) | `97.9% <ø> (ø)` | |  
  
... and [38 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/637/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/637?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/637?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0ab75f9...9ba6ffd](https://app.codecov.io/gh/boostorg/multiprecision/pull/637?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
