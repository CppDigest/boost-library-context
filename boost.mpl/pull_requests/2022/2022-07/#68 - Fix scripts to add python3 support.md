# #68 Fix scripts to add python3 support [Merged]

> Username: thomas955  
> Created at: 2022-07-21 00:19:51 UTC  
> Updated at: 2025-07-03 03:52:09 UTC  
> Merged at: 2025-07-03 03:52:03 UTC  
> Closed at: 2025-07-03 03:52:03 UTC  
> Url: https://github.com/boostorg/mpl/pull/68  

* fixed libraries include  
  * fixed incorrect usage of python2 string  
  * fixed python2 to python3 script issues (2to3) like print ""  
  * fixed data types  
  * fixed python3 usage of str instead of string (join, split etc.)  
  * added support to determine the absolute path of the executable  
    binary of the current Python interpreter  
    * thx @Bagira80 for his support  
  
Fixes issue: #67  
  
Signed-off-by: Thomas Höhlig <thoehlig@benocs.com>

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-02 04:50:50 UTC  
> Updated_at: 2025-07-03 03:52:09 UTC  
> Url: https://github.com/boostorg/mpl/pull/68#issuecomment-1202014489  

## [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/68?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.73%. Comparing base [(`db09fb1`)](https://app.codecov.io/gh/boostorg/mpl/commit/db09fb1dce010e172072a1ba62858b2155285444?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3b2748d`)](https://app.codecov.io/gh/boostorg/mpl/commit/3b2748d85275b49aa9e05fb1418bb71b17b9b916?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 27 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mpl/pull/68/graphs/tree.svg?width=650&height=150&src=pr&token=31bBblRchG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mpl/pull/68?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #68   +/-   ##  
========================================  
  Coverage    94.73%   94.73%             
========================================  
  Files            5        5             
  Lines           19       19             
========================================  
  Hits            18       18             
  Misses           1        1             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mpl/pull/68?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mpl/pull/68?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [db09fb1...3b2748d](https://app.codecov.io/gh/boostorg/mpl/pull/68?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
