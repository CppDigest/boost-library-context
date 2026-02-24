# #1176 Document logpdf, logcdf, and specializations [Merged]

> Username: mborland  
> Created at: 2024-08-12 14:00:57 UTC  
> Updated at: 2024-08-13 18:18:46 UTC  
> Merged at: 2024-08-13 18:18:43 UTC  
> Closed at: 2024-08-13 18:18:43 UTC  
> Url: https://github.com/boostorg/math/pull/1176  

@jzmaddock and @dschmitz89 let me know what you think of these additions.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-08-12 14:56:42 UTC  
> Url: https://github.com/boostorg/math/pull/1176#issuecomment-2284210027  

Thanks @mborland that reads great, we also need some reference docs in non_members.qbk, the `__usual_accessors` macro needs updating, and it would be good to have some some boilerplate text to add to each individual distribution's docs after the __usual_accessors macro to indicate whether the logcdf/pdf accessors add anything that log(cdf) doesn't.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-08-12 15:05:17 UTC  
> Updated_at: 2024-08-12 15:46:52 UTC  
> Url: https://github.com/boostorg/math/pull/1176#issuecomment-2284230423  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1176?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.08%. Comparing base [(`66becad`)](https://app.codecov.io/gh/boostorg/math/commit/66becad28e873477154c57d6721b274fe21db8a3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3714b9e`)](https://app.codecov.io/gh/boostorg/math/commit/3714b9e5a0ff88c7956faaf66fd8c39a6bd1ea09?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 29 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1176/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1176?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1176      +/-   ##  
===========================================  
+ Coverage    94.06%   94.08%   +0.01%       
===========================================  
  Files          780      780                
  Lines        65796    65796                
===========================================  
+ Hits         61891    61903      +12       
+ Misses        3905     3893      -12       
```  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1176/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1176?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1176?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [66becad...3714b9e](https://app.codecov.io/gh/boostorg/math/pull/1176?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: dschmitz89  
> Created_at: 2024-08-13 07:26:46 UTC  
> Updated_at: 2024-08-13 07:27:35 UTC  
> Url: https://github.com/boostorg/math/pull/1176#issuecomment-2285538251  

This looks great thanks.

---

## Comment 4

> Username: mborland  
> Created_at: 2024-08-13 14:02:31 UTC  
> Url: https://github.com/boostorg/math/pull/1176#issuecomment-2286338031  

> This looks great thanks.  
  
Formulas have been added in https://github.com/boostorg/math/pull/1176/commits/65096bee14b66788eba3694fc7d317aa8854d845.

---
