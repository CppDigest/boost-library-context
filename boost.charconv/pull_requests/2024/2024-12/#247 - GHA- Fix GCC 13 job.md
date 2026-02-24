# #247 GHA: Fix GCC 13 job [Merged]

> Username: Flamefire  
> Created at: 2024-12-28 15:59:44 UTC  
> Updated at: 2025-01-02 15:02:22 UTC  
> Merged at: 2025-01-02 13:48:14 UTC  
> Closed at: 2025-01-02 13:48:14 UTC  
> Url: https://github.com/boostorg/charconv/pull/247  

`cxxflags` must be a string type not an array or it will be expanded as:  
```  
  if [ -n "Array" ]  
  then  
      B2_ARGS+=("cxxflags=Array")  
  fi  
```  
  
Fixes #246

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-28 16:35:21 UTC  
> Updated_at: 2025-01-02 13:48:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/247#issuecomment-2564375623  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/247?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.76%. Comparing base [(`8fbdb8a`)](https://app.codecov.io/gh/boostorg/charconv/commit/8fbdb8a08bf0d926b61dfdfea2872f019c8d3608?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`135d9d7`)](https://app.codecov.io/gh/boostorg/charconv/commit/135d9d7815bb29a3eec416f8d7827ef6c10600ee?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 9 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/247/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/247?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #247   +/-   ##  
========================================  
  Coverage    94.76%   94.76%             
========================================  
  Files           69       69             
  Lines         9031     9031             
========================================  
  Hits          8558     8558             
  Misses         473      473             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/247?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/247?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8fbdb8a...135d9d7](https://app.codecov.io/gh/boostorg/charconv/pull/247?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Approved]

> Username: mborland  
> Created_at: 2025-01-02 13:48:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/charconv/pull/247#pullrequestreview-2527601606  

Good Catch

---
