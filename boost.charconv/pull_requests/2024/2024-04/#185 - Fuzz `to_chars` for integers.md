# #185 Fuzz `to_chars` for integers [Merged]

> Username: mborland  
> Created at: 2024-04-18 09:39:40 UTC  
> Updated at: 2024-04-19 06:09:08 UTC  
> Merged at: 2024-04-19 06:09:05 UTC  
> Closed at: 2024-04-19 06:09:05 UTC  
> Url: https://github.com/boostorg/charconv/pull/185  

Fixes buffer overflows since the calculations of the user buffer size did not account for a negative sign.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-04-18 10:18:02 UTC  
> Updated_at: 2024-04-18 10:33:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/185#issuecomment-2063523496  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/185?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.17%. Comparing base [(`552b5b5`)](https://app.codecov.io/gh/boostorg/charconv/commit/552b5b5be091d67426ab1c47af6857c82e5cdc04?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2ba5773`)](https://app.codecov.io/gh/boostorg/charconv/pull/185?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/185/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #185   +/-   ##  
========================================  
  Coverage    93.17%   93.17%             
========================================  
  Files           65       65             
  Lines         8396     8396             
========================================  
  Hits          7823     7823             
  Misses         573      573             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/185?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/charconv/detail/to\_chars\_integer\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/185?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fto_chars_integer_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvdG9fY2hhcnNfaW50ZWdlcl9pbXBsLmhwcA==) | `96.89% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/185?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/185?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [552b5b5...2ba5773](https://app.codecov.io/gh/boostorg/charconv/pull/185?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
