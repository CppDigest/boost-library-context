# #627 Ensure checked integers are in a sane state after a throw. [Merged]

> Username: jzmaddock  
> Created at: 2024-06-23 11:22:44 UTC  
> Updated at: 2024-06-24 14:49:12 UTC  
> Merged at: 2024-06-24 08:12:03 UTC  
> Closed at: 2024-06-24 08:12:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/627  

Document that the value of the target operand is unspecified after a throw. Improve testing.  
Fixes https://github.com/boostorg/multiprecision/issues/626

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-23 11:56:39 UTC  
> Updated_at: 2024-06-23 15:38:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/627#issuecomment-2184959513  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/627?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`58b3962`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/58b396288e20883f527c6da6bb4f45edbdeb958b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`5926513`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/59265137308ff729f74d9f365b175ae27f5b755d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/627/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/627?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #627     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          275     276      +1       
  Lines        26794   26846     +52       
=========================================  
+ Hits         25188   25240     +52       
  Misses        1606    1606               
```  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/627?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/627?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50LmhwcA==) | `95.5% <100.0%> (+0.1%)` | :arrow_up: |  
| [test/git\_issue\_624.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/627?src=pr&el=tree&filepath=test%2Fgit_issue_624.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNjI0LmNwcA==) | `100.0% <100.0%> (ø)` | |  
| [test/git\_issue\_626.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/627?src=pr&el=tree&filepath=test%2Fgit_issue_626.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNjI2LmNwcA==) | `100.0% <100.0%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/627?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/627?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [58b3962...5926513](https://app.codecov.io/gh/boostorg/multiprecision/pull/627?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
