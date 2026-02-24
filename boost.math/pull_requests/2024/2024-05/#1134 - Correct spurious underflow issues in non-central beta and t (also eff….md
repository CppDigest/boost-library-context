# #1134 Correct spurious underflow issues in non-central beta and t (also eff… [Merged]

> Username: jzmaddock  
> Created at: 2024-05-16 17:29:11 UTC  
> Updated at: 2024-05-17 16:54:43 UTC  
> Merged at: 2024-05-17 16:42:04 UTC  
> Closed at: 2024-05-17 16:42:04 UTC  
> Url: https://github.com/boostorg/math/pull/1134  

…ects nc-F via beta).  
  
See https://github.com/scipy/scipy/issues/20693.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-17 10:25:10 UTC  
> Updated_at: 2024-05-17 15:51:30 UTC  
> Url: https://github.com/boostorg/math/pull/1134#issuecomment-2117251271  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1134?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.69%. Comparing base [(`a53b013`)](https://app.codecov.io/gh/boostorg/math/commit/a53b013c735caa98179532a32ad24d34569b9710?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`92f5e2b`)](https://app.codecov.io/gh/boostorg/math/pull/1134?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1134/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1134?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1134      +/-   ##  
===========================================  
- Coverage    93.71%   93.69%   -0.03%       
===========================================  
  Files          771      771                
  Lines        61105    61156      +51       
===========================================  
+ Hits         57264    57299      +35       
- Misses        3841     3857      +16       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1134?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/math/distributions/non\_central\_beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1134?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_beta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfYmV0YS5ocHA=) | `91.79% <100.00%> (+0.08%)` | :arrow_up: |  
| [include/boost/math/distributions/non\_central\_t.hpp](https://app.codecov.io/gh/boostorg/math/pull/1134?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfdC5ocHA=) | `97.64% <100.00%> (-0.12%)` | :arrow_down: |  
| [test/test\_nc\_t.hpp](https://app.codecov.io/gh/boostorg/math/pull/1134?src=pr&el=tree&filepath=test%2Ftest_nc_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X25jX3QuaHBw) | `99.02% <100.00%> (+0.04%)` | :arrow_up: |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1134/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1134?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1134?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a53b013...92f5e2b](https://app.codecov.io/gh/boostorg/math/pull/1134?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-05-17 11:42:58 UTC  
> Url: https://github.com/boostorg/math/pull/1134#issuecomment-2117413812  

@mborland can you see why the codecov report is failing?  All the changed lines seem to be covered.

---

## Comment 3

> Username: mborland  
> Created_at: 2024-05-17 11:50:02 UTC  
> Url: https://github.com/boostorg/math/pull/1134#issuecomment-2117424882  

> @mborland can you see why the codecov report is failing? All the changed lines seem to be covered.  
  
It looks like there were some large indirect changes: https://app.codecov.io/gh/boostorg/math/pull/1134/indirect-changes/. Did your diffs change the regions `expm1` are tested in? Doesn't look like it to me. If they all look benign to you merging will re-baseline the total project coverage.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-05-17 16:41:57 UTC  
> Url: https://github.com/boostorg/math/pull/1134#issuecomment-2117983433  

> It looks like there were some large indirect changes: https://app.codecov.io/gh/boostorg/math/pull/1134/indirect-changes/. Did your diffs change the regions expm1 are tested in? Doesn't look like it to me. If they all look benign to you merging will re-baseline the total project coverage.  
  
I don't see how, and I certainly didn't effect the barycentic_rational code... merging.

---
