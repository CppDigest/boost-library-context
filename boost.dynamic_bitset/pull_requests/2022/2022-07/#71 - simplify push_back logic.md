# #71 simplify push_back logic [Merged]

> Username: ghost  
> Created at: 2022-07-05 22:04:54 UTC  
> Updated at: 2024-12-28 21:25:50 UTC  
> Merged at: 2024-12-28 21:25:42 UTC  
> Closed at: 2024-12-28 21:25:42 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/71  

Hello boost devs/maintainers,  
  
I find the current `push_back` logic a bit strange with the two-steps  
- call `resize()` method but using the default value = false  
- then call `set()` method to explicitly set the bit  
  
I feel it would make sense to simplify this with a single step and reduce overhead  
- call `resize()` method with the given bit  
  
I'm not sure if this is intended, but if not, here is my pull request to address the issue.  
  
Thanks.

---

## Comment 1

> Username: jeking3  
> Created_at: 2024-11-26 20:43:27 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/71#issuecomment-2501899779  

Please rebase this on the current develop branch so that we can run the full set of tests against it.  Thanks.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-12-24 15:09:20 UTC  
> Updated_at: 2024-12-28 21:25:50 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/71#issuecomment-2561218091  

## [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/71?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.53%. Comparing base [(`d4be7a4`)](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/d4be7a4fcbee219f5678a3eea81f1a814a7cb5d3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`237dafa`)](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/237dafae96ed2f1d015adc46cd8e00147523a531?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 22 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/71/graphs/tree.svg?width=650&height=150&src=pr&token=PVG5jth1ez&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/71?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #71      +/-   ##  
===========================================  
- Coverage    95.80%   95.53%   -0.28%       
===========================================  
  Files            5        5                
  Lines          668      672       +4       
  Branches         0      210     +210       
===========================================  
+ Hits           640      642       +2       
- Misses          28       29       +1       
- Partials         0        1       +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/71?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/71?src=pr&el=tree&filepath=include%2Fboost%2Fdynamic_bitset%2Fdynamic_bitset.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `95.19% <100.00%> (-0.30%)` | :arrow_down: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/71/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/71?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/71?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cd59d32...237dafa](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/71?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
