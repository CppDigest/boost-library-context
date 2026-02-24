# #1146 Make boost::math::ccmath::fminf and boost::math::ccmath::fminl constexpr [Merged]

> Username: quxflux  
> Created at: 2024-06-14 07:17:40 UTC  
> Updated at: 2024-06-15 06:30:51 UTC  
> Merged at: 2024-06-14 13:59:20 UTC  
> Closed at: 2024-06-14 13:59:20 UTC  
> Url: https://github.com/boostorg/math/pull/1146  

The publicly visible declarations of `boost::math::ccmath::fminf` and `boost::math::ccmath::fminfl` are neither marked `inline` nor `constexpr`, which causes "multiple definition" linker errors when the `boost/math/ccmath/fmin.hpp` header gets included in multiple translation units of a binary.  
  
Therefore this PR marks said functions `constexpr`.  
  
Fixes #1145

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2024-06-14 12:01:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1146#pullrequestreview-2118217365  

LGTM. Approved the CI run. Thanks!

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-06-14 12:09:12 UTC  
> Updated_at: 2024-06-14 13:58:11 UTC  
> Url: https://github.com/boostorg/math/pull/1146#issuecomment-2167886105  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1146?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.76%. Comparing base [(`70cdb37`)](https://app.codecov.io/gh/boostorg/math/commit/70cdb37759870beb2d6bee5d79d9787821ac5561?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`dff806a`)](https://app.codecov.io/gh/boostorg/math/commit/dff806a50a30bd97797c0eb37100134947e330f1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1146/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1146      +/-   ##  
===========================================  
+ Coverage    93.71%   93.76%   +0.05%       
===========================================  
  Files          774      772       -2       
  Lines        61271    61251      -20       
===========================================  
+ Hits         57422    57434      +12       
+ Misses        3849     3817      -32       
```  
  
  
[see 3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1146/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1146?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1146?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [70cdb37...dff806a](https://app.codecov.io/gh/boostorg/math/pull/1146?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
