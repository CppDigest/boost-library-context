# #284 Fix quadmath detection in CMake for Cross-Compilation [Merged]

> Username: mborland  
> Created at: 2025-10-27 09:05:39 UTC  
> Updated at: 2025-10-29 13:38:38 UTC  
> Merged at: 2025-10-29 13:38:30 UTC  
> Closed at: 2025-10-29 13:38:30 UTC  
> Url: https://github.com/boostorg/charconv/pull/284  

@anarthal Can you please see if this change breaks your alpine environment? I added the CheckCXXSourceRuns to fix that environment but now apparently this breaks cross compilation: https://github.com/boostorg/charconv/pull/193#issuecomment-3438932701

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-27 09:47:44 UTC  
> Updated_at: 2025-10-29 13:38:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/284#issuecomment-3450384646  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/284?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 94.84%. Comparing base ([`0c8358a`](https://app.codecov.io/gh/boostorg/charconv/commit/0c8358acfa37632f4174c582bb22e010824043ef?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`b024cfc`](https://app.codecov.io/gh/boostorg/charconv/commit/b024cfc8da083bcadf4000cc173425fbc40a864e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/284/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/284?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #284      +/-   ##  
===========================================  
- Coverage    94.92%   94.84%   -0.08%       
===========================================  
  Files           73       73                
  Lines         9186     8986     -200       
===========================================  
- Hits          8720     8523     -197       
+ Misses         466      463       -3       
```  
[see 47 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/charconv/pull/284/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/284?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/284?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0c8358a...b024cfc](https://app.codecov.io/gh/boostorg/charconv/pull/284?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-10-29 13:03:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/284#issuecomment-3461408528  

Seems to be fine: https://github.com/anarthal/servertech-chat/actions/runs/18907969430/job/53970789363

---

## Comment 3

> Username: mborland  
> Created_at: 2025-10-29 13:37:48 UTC  
> Url: https://github.com/boostorg/charconv/pull/284#issuecomment-3461573754  

> Seems to be fine: https://github.com/anarthal/servertech-chat/actions/runs/18907969430/job/53970789363  
  
Cool. Thanks for checking.

---
