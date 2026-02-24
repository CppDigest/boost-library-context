# #649 Try and optimize small integer powers. [Merged]

> Username: jzmaddock  
> Created at: 2025-01-17 14:41:36 UTC  
> Updated at: 2025-01-17 19:41:36 UTC  
> Merged at: 2025-01-17 17:37:24 UTC  
> Closed at: 2025-01-17 17:37:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/649  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-17 15:22:26 UTC  
> Updated_at: 2025-01-17 17:37:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/649#issuecomment-2598599938  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/649?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`ad1ee2a`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/ad1ee2a9a8daf6fee2583f4dd98a15237f8e413f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`d606acf`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/d606acf98e6e86f86d99e28d534c7455432f45bc?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/649/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/649?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #649     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          279     279               
  Lines        28885   28903     +18       
=========================================  
+ Hits         27161   27179     +18       
  Misses        1724    1724               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/649?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/multiprecision/detail/functions/pow.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/649?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Ffunctions%2Fpow.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZnVuY3Rpb25zL3Bvdy5ocHA=) | `93.0% <100.0%> (+0.3%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/649?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/649?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ad1ee2a...d606acf](https://app.codecov.io/gh/boostorg/multiprecision/pull/649?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-01-17 17:49:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/649#issuecomment-2598886166  

Thank you John!

---

## Comment 3

> Username: cosurgi  
> Created_at: 2025-01-17 19:41:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/649#issuecomment-2599072622  

Hi John (@jzmaddock), if it's possible please add a `default:` case which does nothing. I am compiling YADE with flags (among others) `-Werror -Wswitch-default`. This will produce a compilation error when `default:` is missing

---
