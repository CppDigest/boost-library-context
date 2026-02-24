# #113 BOOST_ASSERT usage #112 [Merged]

> Username: ygeunkim  
> Created at: 2024-10-23 13:25:28 UTC  
> Updated at: 2024-10-23 18:46:42 UTC  
> Merged at: 2024-10-23 18:46:17 UTC  
> Closed at: 2024-10-23 18:46:17 UTC  
> Url: https://github.com/boostorg/random/pull/113  

- Use BOOST_ASSERT instead of a standard assert  
- Leave `assert` in test code in this stage  
- Related to #112

---

## Comment 1

> Username: mborland  
> Created_at: 2024-10-23 13:36:50 UTC  
> Url: https://github.com/boostorg/random/pull/113#issuecomment-2432221728  

Thanks @ygeunkim. Let me know when you think this is good to go since it's marked as draft. I'll run the CI and review it.

---

## Comment 2

> Username: ygeunkim  
> Created_at: 2024-10-23 13:52:00 UTC  
> Url: https://github.com/boostorg/random/pull/113#issuecomment-2432279459  

Thanks @mborland! I've changed `assert` to `BOOST_ASSERT` in beta_distribution.hpp and hyperexponential_distribution.hpp mentioned in the issue #112, while didn't touch the standard assert in test codes just in case.

---

## Review 3 [Changes requested]

> Username: mborland  
> Created_at: 2024-10-23 13:58:21 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/random/pull/113#pullrequestreview-2388840683  

I would add `#include <boost/assert.hpp>` to both of these. Even if the header is transitively pulled it it's better to be explicit.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-10-23 15:18:21 UTC  
> Updated_at: 2024-10-23 18:46:42 UTC  
> Url: https://github.com/boostorg/random/pull/113#issuecomment-2432593899  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/113?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.91%. Comparing base [(`dcaee17`)](https://app.codecov.io/gh/boostorg/random/commit/dcaee178a68566bbe50c5454a8ff3db67412055e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ccd9721`)](https://app.codecov.io/gh/boostorg/random/commit/ccd9721db0b86f9d6ffb92b0d31e75eede562837?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/113/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/113?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #113      +/-   ##  
===========================================  
+ Coverage    94.84%   94.91%   +0.07%       
===========================================  
  Files          101      101                
  Lines         6242     6644     +402       
===========================================  
+ Hits          5920     6306     +386       
- Misses         322      338      +16       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/113?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/beta\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/113?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fbeta_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vYmV0YV9kaXN0cmlidXRpb24uaHBw) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/random/hyperexponential\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/113?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fhyperexponential_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vaHlwZXJleHBvbmVudGlhbF9kaXN0cmlidXRpb24uaHBw) | `95.42% <100.00%> (+0.46%)` | :arrow_up: |  
  
... and [79 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/random/pull/113/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/113?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/113?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [dcaee17...ccd9721](https://app.codecov.io/gh/boostorg/random/pull/113?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
