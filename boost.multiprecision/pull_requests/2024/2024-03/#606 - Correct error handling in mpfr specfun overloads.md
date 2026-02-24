# #606 Correct error handling in mpfr specfun overloads. [Merged]

> Username: jzmaddock  
> Created at: 2024-03-01 13:13:39 UTC  
> Updated at: 2024-03-02 15:39:25 UTC  
> Merged at: 2024-03-02 10:14:41 UTC  
> Closed at: 2024-03-02 10:14:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/606  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-03-01 15:08:44 UTC  
> Updated_at: 2024-03-01 19:13:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/606#issuecomment-1973361134  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/606?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`42a3edf`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/42a3edf55ee8fc5569e033e44a6f263e4453b863?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ab15f2f`)](https://app.codecov.io/gh/boostorg/multiprecision/pull/606?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
> :exclamation: Current head ab15f2f differs from pull request most recent head fe60def. Consider uploading reports for the commit fe60def to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/606/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/606?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #606     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          274     274               
  Lines        28563   28563               
=========================================  
+ Hits         26872   26874      +2       
+ Misses        1691    1689      -2       
```  
  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/606/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/606?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/606?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [42a3edf...fe60def](https://app.codecov.io/gh/boostorg/multiprecision/pull/606?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-03-02 10:14:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/606#issuecomment-1974754778  

Last failure is a network timeout on drone.  
  
@mborland with the changes in Math develop, this now gets Multiprecision develop green and good to go.  Math will need a second merge to master before Multiprecision.

---
