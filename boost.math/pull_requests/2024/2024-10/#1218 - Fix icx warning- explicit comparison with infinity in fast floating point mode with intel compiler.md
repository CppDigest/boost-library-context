# #1218 Fix icx warning: explicit comparison with infinity in fast floating point mode with intel compiler [Merged]

> Username: pps83  
> Created at: 2024-10-25 00:52:20 UTC  
> Updated at: 2024-10-28 19:49:52 UTC  
> Merged at: 2024-10-28 19:46:58 UTC  
> Closed at: 2024-10-28 19:46:59 UTC  
> Url: https://github.com/boostorg/math/pull/1218  

fixes #1217

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-10-25 13:17:37 UTC  
> Updated_at: 2024-10-28 19:49:52 UTC  
> Url: https://github.com/boostorg/math/pull/1218#issuecomment-2437756966  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1218?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.78%. Comparing base [(`097b73c`)](https://app.codecov.io/gh/boostorg/math/commit/097b73c9bfe7528dec04cf144d9ebaf7d9d97882?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1532d1a`)](https://app.codecov.io/gh/boostorg/math/commit/1532d1aec936d9dc6f9322cc77944ef5aaaedc8f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1218/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #1218    +/-   ##  
=========================================  
  Coverage    93.77%   93.78%              
=========================================  
  Files          657      654     -3       
  Lines        55285    55090   -195       
=========================================  
- Hits         51846    51664   -182       
+ Misses        3439     3426    -13       
```  
  
[see 4 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1218/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1218?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1218?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [097b73c...1532d1a](https://app.codecov.io/gh/boostorg/math/pull/1218?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Approved]

> Username: mborland  
> Created_at: 2024-10-25 13:20:59 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1218#pullrequestreview-2395280875  

This looks fine to me. I'm not sure when `-Wtautological-constant-compare` was added but as long as CI is green it should be OK. I don't think we'd gain anything from restricting this to `__INTEL_LLVM_COMPILER` in case base clang picks up the behavior.

---

## Comment 3

> Username: pps83  
> Created_at: 2024-10-26 19:52:34 UTC  
> Url: https://github.com/boostorg/math/pull/1218#issuecomment-2439718321  

> __INTEL_LLVM_COMPILER  
  
Yes, `__INTEL_LLVM_COMPILER` can be used to check for new intel compiler only. Let me know if I should replace the checks

---

## Comment 4

> Username: pps83  
> Created_at: 2024-10-28 17:15:31 UTC  
> Url: https://github.com/boostorg/math/pull/1218#issuecomment-2442175267  

> This looks fine to me. I'm not sure when `-Wtautological-constant-compare` was added but as long as CI is green it should be OK. I don't think we'd gain anything from restricting this to `__INTEL_LLVM_COMPILER` in case base clang picks up the behavior.  
  
I updated it with `__clang__major__` >= 6 check based on this code: https://github.com/zouyonghao/DistFuzz/blob/1022471d1883e38ba4d26f9da29a9aa73280b0ff/strace/src/gcc_compat.h#L153

---

## Comment 5

> Username: mborland  
> Created_at: 2024-10-28 17:26:13 UTC  
> Url: https://github.com/boostorg/math/pull/1218#issuecomment-2442197829  

> > This looks fine to me. I'm not sure when `-Wtautological-constant-compare` was added but as long as CI is green it should be OK. I don't think we'd gain anything from restricting this to `__INTEL_LLVM_COMPILER` in case base clang picks up the behavior.  
>   
> I updated it with `__clang__major__` >= 6 check based on this code: https://github.com/zouyonghao/DistFuzz/blob/1022471d1883e38ba4d26f9da29a9aa73280b0ff/strace/src/gcc_compat.h#L153  
  
I approved the CI run and will merge once we get a result. Since we require C++14 I don't think anything below Clang 6 works anyway but it doesn't hurt to have the check.

---
