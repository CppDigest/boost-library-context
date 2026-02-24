# #276 Fix log2 computation [Merged]

> Username: mborland  
> Created at: 2025-07-18 14:20:22 UTC  
> Updated at: 2025-07-21 15:57:15 UTC  
> Merged at: 2025-07-21 15:57:12 UTC  
> Closed at: 2025-07-21 15:57:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/276  

Closes: #275

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-07-18 14:59:15 UTC  
> Updated_at: 2025-07-19 01:18:58 UTC  
> Url: https://github.com/boostorg/charconv/pull/276#issuecomment-3089776754  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/276?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.89%. Comparing base [(`e39c280`)](https://app.codecov.io/gh/boostorg/charconv/commit/e39c280c7e1bc7cef68f566077f83fe204137eb1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`5e2133a`)](https://app.codecov.io/gh/boostorg/charconv/commit/5e2133a99c214a3a5e2fee0442be17c90ff44079?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/276/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/276?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #276   +/-   ##  
========================================  
  Coverage    94.89%   94.89%             
========================================  
  Files           71       71             
  Lines         9123     9123             
========================================  
  Hits          8657     8657             
  Misses         466      466             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/276?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/charconv/detail/from\_chars\_integer\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/276?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Ffrom_chars_integer_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZnJvbV9jaGFyc19pbnRlZ2VyX2ltcGwuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/276?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/276?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e39c280...5e2133a](https://app.codecov.io/gh/boostorg/charconv/pull/276?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
