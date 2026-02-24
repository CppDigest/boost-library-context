# #609 Type which are convertible to a number should not participate in arit… [Merged]

> Username: jzmaddock  
> Created at: 2024-03-06 19:16:23 UTC  
> Updated at: 2024-03-08 09:07:23 UTC  
> Merged at: 2024-03-07 17:23:49 UTC  
> Closed at: 2024-03-07 17:23:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/609  

…hmetic operator overloads.  
  
Fixes https://github.com/boostorg/multiprecision/issues/608

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-03-07 11:12:37 UTC  
> Updated_at: 2024-03-07 11:24:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/609#issuecomment-1983286776  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/609?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`3c9ff4d`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/3c9ff4d6ff9acbb47760c6ceca2d85648b44ca84?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`4fe16b7`)](https://app.codecov.io/gh/boostorg/multiprecision/pull/609?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/609/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/609?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #609     +/-   ##  
=========================================  
- Coverage     94.1%   94.1%   -0.0%       
=========================================  
  Files          274     274               
  Lines        28563   28563               
=========================================  
- Hits         26874   26872      -2       
- Misses        1689    1691      +2       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/609?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/multiprecision/detail/number\_base.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/609?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvbnVtYmVyX2Jhc2UuaHBw) | `98.0% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/609/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/609?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/609?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3c9ff4d...4fe16b7](https://app.codecov.io/gh/boostorg/multiprecision/pull/609?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-03-07 17:41:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/609#issuecomment-1984096777  

Should we ask Marshall about merging this into master once it's permissible again?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-03-08 09:07:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/609#issuecomment-1985317679  

Probably yes.

---
