# #73 Fix BOOST_MPL_AUX_ASSERT_NOT_NA [Merged]

> Username: aihao1007  
> Created at: 2022-10-12 02:31:27 UTC  
> Updated at: 2025-01-03 20:33:07 UTC  
> Merged at: 2025-01-03 20:32:31 UTC  
> Closed at: 2025-01-03 20:32:31 UTC  
> Url: https://github.com/boostorg/mpl/pull/73  



---

## Comment 1

> Username: mclow  
> Created_at: 2022-10-12 03:31:58 UTC  
> Url: https://github.com/boostorg/mpl/pull/73#issuecomment-1275536175  

This looks reasonable to me.  Are there any tests that are currently failing that this will fix? If not, should there be?

---

## Comment 2

> Username: aihao1007  
> Created_at: 2022-10-12 03:55:01 UTC  
> Url: https://github.com/boostorg/mpl/pull/73#issuecomment-1275549913  

BOOST_MPL_AUX_ASSERT_NOT_NA macro parameters is "x" ,"type" is undefined.

---

## Comment 3

> Username: mclow  
> Created_at: 2022-10-12 05:04:33 UTC  
> Url: https://github.com/boostorg/mpl/pull/73#issuecomment-1275592895  

> BOOST_MPL_AUX_ASSERT_NOT_NA macro parameters is "x" ,"type" is undefined.  
  
I'm not disagreeing; I'm wondering how this is failing.

---

## Comment 4

> Username: aihao1007  
> Created_at: 2022-10-12 06:19:51 UTC  
> Url: https://github.com/boostorg/mpl/pull/73#issuecomment-1275649465  

When I use BOOST_MPL_AUX_ASSERT_NOT_NA alone to wirte a test code,I discover this error. However, the macro is right when is uesd by the mpl to define "type".

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2022-10-12 10:31:07 UTC  
> Updated_at: 2025-01-03 20:33:07 UTC  
> Url: https://github.com/boostorg/mpl/pull/73#issuecomment-1275945945  

## [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/73?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.73%. Comparing base [(`db09fb1`)](https://app.codecov.io/gh/boostorg/mpl/commit/db09fb1dce010e172072a1ba62858b2155285444?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b27b650`)](https://app.codecov.io/gh/boostorg/mpl/commit/b27b650f4af085d0433a305e96817b709e9c79c4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 22 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mpl/pull/73/graphs/tree.svg?width=650&height=150&src=pr&token=31bBblRchG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mpl/pull/73?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #73   +/-   ##  
========================================  
  Coverage    94.73%   94.73%             
========================================  
  Files            5        5             
  Lines           19       19             
========================================  
  Hits            18       18             
  Misses           1        1             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mpl/pull/73?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/73?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [db09fb1...b27b650](https://app.codecov.io/gh/boostorg/mpl/pull/73?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
