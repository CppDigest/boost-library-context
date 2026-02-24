# #83 Do not check the bitand/bitor macros in cplusplus mode [Merged]

> Username: jeking3  
> Created at: 2025-01-03 14:06:26 UTC  
> Updated at: 2025-01-03 16:34:57 UTC  
> Merged at: 2025-01-03 16:34:47 UTC  
> Closed at: 2025-01-03 16:34:47 UTC  
> Url: https://github.com/boostorg/mpl/pull/83  

Some 3rdparty parsers (e.g. Qt moc) fail to parse the bitand.hpp due to the fact that bitand is a keyword in C++, not a macro. Modern MSVC versions define bitand/bitor only if __cplusplus macro is not defined, see iso646.h: https://pastebin.com/zTcd0juT  
  
Thus, boost can also check the macro only in C mode making moc happy

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-03 14:21:56 UTC  
> Updated_at: 2025-01-03 16:34:57 UTC  
> Url: https://github.com/boostorg/mpl/pull/83#issuecomment-2569291160  

## [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/83?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.73%. Comparing base [(`2b7937a`)](https://app.codecov.io/gh/boostorg/mpl/commit/2b7937ac6fb7a5193784225cfb80d4b14f4f1a12?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`478f421`)](https://app.codecov.io/gh/boostorg/mpl/commit/478f421104b09ae419005ce5f656cf2043c166c0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mpl/pull/83/graphs/tree.svg?width=650&height=150&src=pr&token=31bBblRchG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mpl/pull/83?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #83   +/-   ##  
========================================  
  Coverage    94.73%   94.73%             
========================================  
  Files            5        5             
  Lines           19       19             
  Branches         3        3             
========================================  
  Hits            18       18             
  Misses           1        1             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mpl/pull/83?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/83?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2b7937a...478f421](https://app.codecov.io/gh/boostorg/mpl/pull/83?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
