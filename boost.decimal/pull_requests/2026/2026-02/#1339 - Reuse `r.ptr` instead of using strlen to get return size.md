# #1339 Reuse `r.ptr` instead of using strlen to get return size [Merged]

> Username: pps83  
> Created at: 2026-02-14 04:43:25 UTC  
> Updated at: 2026-02-16 17:52:33 UTC  
> Merged at: 2026-02-16 17:51:29 UTC  
> Closed at: 2026-02-16 17:51:29 UTC  
> Url: https://github.com/boostorg/decimal/pull/1339  



---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2026-02-16 14:49:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/decimal/pull/1339#pullrequestreview-3809038980  

Thanks. I kicked off the CI run

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2026-02-16 15:03:27 UTC  
> Updated_at: 2026-02-16 17:52:33 UTC  
> Url: https://github.com/boostorg/decimal/pull/1339#issuecomment-3908964272  

## [Codecov](https://app.codecov.io/gh/boostorg/decimal/pull/1339?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.5%. Comparing base ([`17cfe8b`](https://app.codecov.io/gh/boostorg/decimal/commit/17cfe8b091352ce589e092c8d4ad6b59d2a7d8ca?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`999ce9f`](https://app.codecov.io/gh/boostorg/decimal/commit/999ce9fe5c34950f2ab6785d6f5f0bb4c1acd15c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/decimal/pull/1339/graphs/tree.svg?width=650&height=150&src=pr&token=SkjASiv9Df&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/decimal/pull/1339?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1339     +/-   ##  
=========================================  
- Coverage     98.5%   98.5%   -0.0%       
=========================================  
  Files          282     282               
  Lines        22551   22550      -1       
  Branches      1960    1960               
=========================================  
- Hits         22200   22198      -2       
- Misses         351     352      +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/decimal/pull/1339?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/string.hpp](https://app.codecov.io/gh/boostorg/decimal/pull/1339?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fstring.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL3N0cmluZy5ocHA=) | `100.0% <100.0%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/decimal/pull/1339/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/decimal/pull/1339?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/decimal/pull/1339?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [17cfe8b...999ce9f](https://app.codecov.io/gh/boostorg/decimal/pull/1339?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
