# #603 Workaround for type change in tommath [Merged]

> Username: mborland  
> Created at: 2024-02-28 11:03:02 UTC  
> Updated at: 2024-03-04 17:45:57 UTC  
> Merged at: 2024-03-04 09:39:28 UTC  
> Closed at: 2024-03-04 09:39:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/603  

Closes: #602   
  
@jzmaddock This is certainly not the most robust solution on earth, but I see that we check against similar deprecation macros elsewhere. They have no versioning macro so I can't think of a better solution.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-28 12:28:13 UTC  
> Updated_at: 2024-03-04 09:10:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/603#issuecomment-1968880436  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/603?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`3c9ff4d`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/3c9ff4d6ff9acbb47760c6ceca2d85648b44ca84?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`5675c50`)](https://app.codecov.io/gh/boostorg/multiprecision/pull/603?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/603/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/603?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #603   +/-   ##  
=======================================  
  Coverage     94.1%   94.1%             
=======================================  
  Files          274     274             
  Lines        28563   28563             
=======================================  
  Hits         26874   26874             
  Misses        1689    1689             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/603?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/603?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3c9ff4d...5675c50](https://app.codecov.io/gh/boostorg/multiprecision/pull/603?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-02-28 17:58:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/603#issuecomment-1969541550  

Thanks Matt, this is about all we can do here I think.

---
