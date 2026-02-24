# #651 Suppress warnings caused by use of -Wswitch_default on GCC [Merged]

> Username: jzmaddock  
> Created at: 2025-01-19 15:19:55 UTC  
> Updated at: 2025-01-20 15:24:10 UTC  
> Merged at: 2025-01-20 09:44:22 UTC  
> Closed at: 2025-01-20 09:44:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/651  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-19 15:53:16 UTC  
> Updated_at: 2025-01-20 09:45:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/651#issuecomment-2600920157  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `97.36842%` with `2 lines` in your changes missing coverage. Please review.  
> Project coverage is 94.1%. Comparing base [(`abc70a2`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/abc70a2d8cfc0d647610d7f8f64cc8a6e00af209?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`bd18c8d`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/bd18c8d9ccab18ccc5ad8bf37f0baf6183f3df45?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_bin\_float/io.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_bin_float%2Fio.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfYmluX2Zsb2F0L2lvLmhwcA==) | 0.0% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/651/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #651     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          279     279               
  Lines        28903   28979     +76       
=========================================  
+ Hits         27181   27255     +74       
- Misses        1722    1724      +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_bin\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_bin_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfYmluX2Zsb2F0LmhwcA==) | `89.7% <100.0%> (+0.5%)` | :arrow_up: |  
| [...nclude/boost/multiprecision/detail/default\_ops.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Fdefault_ops.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZGVmYXVsdF9vcHMuaHBw) | `91.1% <100.0%> (+0.2%)` | :arrow_up: |  
| [...lude/boost/multiprecision/detail/functions/pow.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Ffunctions%2Fpow.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZnVuY3Rpb25zL3Bvdy5ocHA=) | `93.0% <100.0%> (+0.1%)` | :arrow_up: |  
| [...ude/boost/multiprecision/detail/functions/trig.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Ffunctions%2Ftrig.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZnVuY3Rpb25zL3RyaWcuaHBw) | `96.4% <100.0%> (+0.1%)` | :arrow_up: |  
| [include/boost/multiprecision/cpp\_bin\_float/io.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_bin_float%2Fio.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfYmluX2Zsb2F0L2lvLmhwcA==) | `86.0% <0.0%> (-0.5%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [abc70a2...bd18c8d](https://app.codecov.io/gh/boostorg/multiprecision/pull/651?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
