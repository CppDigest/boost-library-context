# #87 Fix preprocessed python3 scripts [Merged]

> Username: jeking3  
> Created at: 2025-07-03 04:02:52 UTC  
> Updated at: 2025-07-03 12:55:31 UTC  
> Merged at: 2025-07-03 12:55:00 UTC  
> Closed at: 2025-07-03 12:55:00 UTC  
> Url: https://github.com/boostorg/mpl/pull/87  

https://github.com/boostorg/mpl/pull/68 was incomplete; I finished this for modern python3 and checked that the script can run.  The only incompatible issue was that files were opened with "rU" and the "U" is no longer valid.  I updated the README, but I did not regenerate the files.  When I did, it wouldn't build in CI.  
  
This fixes #67

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-07-03 04:31:13 UTC  
> Updated_at: 2025-07-03 12:55:31 UTC  
> Url: https://github.com/boostorg/mpl/pull/87#issuecomment-3030651699  

## [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/87?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.73%. Comparing base [(`4b81600`)](https://app.codecov.io/gh/boostorg/mpl/commit/4b8160057ad247ec5582876d2cacc3cabab4a164?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`04d2348`)](https://app.codecov.io/gh/boostorg/mpl/commit/04d2348caec396e1065b6d6b8c59846d2b889291?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mpl/pull/87/graphs/tree.svg?width=650&height=150&src=pr&token=31bBblRchG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mpl/pull/87?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #87   +/-   ##  
========================================  
  Coverage    94.73%   94.73%             
========================================  
  Files            5        5             
  Lines           19       19             
  Branches         3        3             
========================================  
  Hits            18       18             
  Misses           1        1             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mpl/pull/87?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/87?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4b81600...04d2348](https://app.codecov.io/gh/boostorg/mpl/pull/87?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
