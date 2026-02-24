# #884 refactor(string_view_base): remove hash_value [Merged]

> Username: alandefreitas  
> Created at: 2024-11-11 19:50:52 UTC  
> Updated at: 2024-11-12 04:04:26 UTC  
> Merged at: 2024-11-12 04:03:57 UTC  
> Closed at: 2024-11-12 04:03:57 UTC  
> Url: https://github.com/boostorg/url/pull/884  

Since https://github.com/boostorg/core/commit/92f6cfb3ccf977b6a5c615ce174a9d94002b3b0d in Boost.Core, we have been getting "error: use of undeclared identifier 'hash_value'" because hash_value is not part of the interface of core::string_view.  
  
hash_value has been removed from core::string_view because it's redundant. Boost containers don't need it and it does not enable std containers.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-11-11 20:25:44 UTC  
> Updated_at: 2024-11-12 04:04:26 UTC  
> Url: https://github.com/boostorg/url/pull/884#issuecomment-2468965505  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/884?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.20%. Comparing base [(`4980f8e`)](https://app.codecov.io/gh/boostorg/url/commit/4980f8ec37984a0928d9f6324d5d6b57b58838d7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`7b24e28`)](https://app.codecov.io/gh/boostorg/url/commit/7b24e28871469feab3880a7f2f3311c3b5765dd8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/884/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/884?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #884   +/-   ##  
========================================  
  Coverage    99.20%   99.20%             
========================================  
  Files          157      157             
  Lines         8415     8415             
========================================  
  Hits          8348     8348             
  Misses          67       67             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/884?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/grammar/string\_view\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/884?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Fstring_view_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9zdHJpbmdfdmlld19iYXNlLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/884?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/884?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [91f81f8...7b24e28](https://app.codecov.io/gh/boostorg/url/pull/884?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
