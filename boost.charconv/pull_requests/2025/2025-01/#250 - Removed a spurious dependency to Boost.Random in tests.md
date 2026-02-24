# #250 Removed a spurious dependency to Boost.Random in tests [Merged]

> Username: anarthal  
> Created at: 2025-01-01 13:50:53 UTC  
> Updated at: 2025-01-02 13:49:52 UTC  
> Merged at: 2025-01-02 13:49:21 UTC  
> Closed at: 2025-01-02 13:49:21 UTC  
> Url: https://github.com/boostorg/charconv/pull/250  

Boost.Random is not used anyhow

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-01 14:25:51 UTC  
> Updated_at: 2025-01-02 13:49:52 UTC  
> Url: https://github.com/boostorg/charconv/pull/250#issuecomment-2567029720  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/250?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.76%. Comparing base [(`8fbdb8a`)](https://app.codecov.io/gh/boostorg/charconv/commit/8fbdb8a08bf0d926b61dfdfea2872f019c8d3608?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`c862a0d`)](https://app.codecov.io/gh/boostorg/charconv/commit/c862a0d8c3a769666d809600066543b1046e3582?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 11 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/250/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/250?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #250   +/-   ##  
========================================  
  Coverage    94.76%   94.76%             
========================================  
  Files           69       69             
  Lines         9031     9031             
========================================  
  Hits          8558     8558             
  Misses         473      473             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/250?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/github\_issue\_152.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/250?src=pr&el=tree&filepath=test%2Fgithub_issue_152.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTUyLmNwcA==) | `98.91% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/250?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/250?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8fbdb8a...c862a0d](https://app.codecov.io/gh/boostorg/charconv/pull/250?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Approved]

> Username: mborland  
> Created_at: 2025-01-02 13:49:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/charconv/pull/250#pullrequestreview-2527603241  

Thanks for this. It was needed when float128 was tested here but that got separated out a while ago.

---
