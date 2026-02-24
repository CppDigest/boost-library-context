# #625 Correct divide-by-zero in cpp_int modulus operations. [Merged]

> Username: jzmaddock  
> Created at: 2024-06-19 18:44:54 UTC  
> Updated at: 2024-06-21 16:16:14 UTC  
> Merged at: 2024-06-21 16:14:54 UTC  
> Closed at: 2024-06-21 16:14:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/625  

Also added some more efficient operator overloads for the trivial case. Fixes https://github.com/boostorg/multiprecision/issues/624

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-21 15:00:08 UTC  
> Updated_at: 2024-06-21 16:11:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/625#issuecomment-2182919934  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/625?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `92.85714%` with `2 lines` in your changes missing coverage. Please review.  
> Project coverage is 94.1%. Comparing base [(`f9fdcc2`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/f9fdcc2b31a01c8e8dc6253339ef223d349a43a2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b0ea443`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/b0ea443759578cbd10ee11570dd53f800ca14d80?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
> :exclamation: **Current head b0ea443 differs from pull request most recent head 891e2c3**  
>   
> Please [upload](https://docs.codecov.com/docs/codecov-uploader) reports for the commit 891e2c3 to get more accurate results.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/625/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/625?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #625     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          274     275      +1       
  Lines        26766   26794     +28       
=========================================  
+ Hits         25161   25188     +27       
- Misses        1605    1606      +1       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/625?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/git\_issue\_624.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/625?src=pr&el=tree&filepath=test%2Fgit_issue_624.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNjI0LmNwcA==) | `100.0% <100.0%> (ø)` | |  
| [include/boost/multiprecision/cpp\_int/divide.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/625?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fdivide.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2RpdmlkZS5ocHA=) | `92.5% <83.4%> (-<0.1%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/625?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/625?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f9fdcc2...891e2c3](https://app.codecov.io/gh/boostorg/multiprecision/pull/625?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
