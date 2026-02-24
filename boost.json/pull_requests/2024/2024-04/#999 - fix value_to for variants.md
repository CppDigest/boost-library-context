# #999 fix value_to for variants [Merged]

> Username: grisumbras  
> Created at: 2024-04-03 12:23:05 UTC  
> Updated at: 2024-04-03 13:47:05 UTC  
> Merged at: 2024-04-03 13:47:02 UTC  
> Closed at: 2024-04-03 13:47:02 UTC  
> Url: https://github.com/boostorg/json/pull/999  

After we allowed exceptions to propagate through error_code-based conversions (when the user invoked value_to, rather than try_value_to), we inadvertently broke value_to for variants, because they relied on exceptions being caught by try_value_to. This change disables exception propagation for exactly one level of nesting.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-04-03 12:57:52 UTC  
> Url: https://github.com/boostorg/json/pull/999#issuecomment-2034536964  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/999?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.08%. Comparing base [(`3a7ee79`)](https://app.codecov.io/gh/boostorg/json/commit/3a7ee79a74c788c2ba400be3ef58712b8ae94939?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9f85ed6`)](https://app.codecov.io/gh/boostorg/json/pull/999?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/999/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/999?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #999   +/-   ##  
========================================  
  Coverage    93.08%   93.08%             
========================================  
  Files           87       87             
  Lines         8125     8125             
========================================  
  Hits          7563     7563             
  Misses         562      562             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/999?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/999?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fvalue_to.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/999?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/999?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3a7ee79...9f85ed6](https://app.codecov.io/gh/boostorg/json/pull/999?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-04-03 13:42:11 UTC  
> Url: https://github.com/boostorg/json/pull/999#issuecomment-2034651845  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/999/pullrequest-condensed-16a6cc6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/999/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/999/pullrequest.html)

---
