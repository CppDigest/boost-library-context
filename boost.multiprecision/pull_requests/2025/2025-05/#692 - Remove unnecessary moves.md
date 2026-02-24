# #692 Remove unnecessary moves [Merged]

> Username: pps83  
> Created at: 2025-05-28 09:29:04 UTC  
> Updated at: 2025-05-28 19:44:03 UTC  
> Merged at: 2025-05-28 12:22:07 UTC  
> Closed at: 2025-05-28 12:22:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/692  

Fixes these warnings:  
```  
../../../boost/multiprecision/detail/default_ops.hpp:2846:47: warning: moving a temporary object prevents copy elision [-Wpessimizing-move]  
 2846 |    return std::move(trunc(number_type(v), pol));  
      |  
```

---

## Comment 1

> Username: pps83  
> Created_at: 2025-05-28 09:31:32 UTC  
> Updated_at: 2025-05-28 09:31:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/692#issuecomment-2915632811  

sample output showing these warnings: https://github.com/boostorg/multiprecision/actions/runs/15286632405/job/42998188606?pr=691#step:16:3577

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-05-28 10:02:44 UTC  
> Updated_at: 2025-05-28 12:22:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/692#issuecomment-2915732029  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/692?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`eef4acf`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/eef4acfbe100cdbbf1ff9dc04491d52cc14c0fe1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8d4c9da`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/8d4c9dac53a86db203ebe49763cefda31683d304?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 49 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/692/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/692?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #692     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          279     280      +1       
  Lines        28979   29074     +95       
=========================================  
+ Hits         27253   27347     +94       
- Misses        1726    1727      +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/692?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/multiprecision/detail/default\_ops.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/692?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Fdefault_ops.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZGVmYXVsdF9vcHMuaHBw) | `91.1% <100.0%> (ø)` | |  
  
... and [8 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/692/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/692?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/692?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [eef4acf...8d4c9da](https://app.codecov.io/gh/boostorg/multiprecision/pull/692?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-05-28 12:22:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/692#issuecomment-2916099581  

Looks good to me, thanks for this!  
  
CI failures are unrelated, I'll investigate those separately.

---
