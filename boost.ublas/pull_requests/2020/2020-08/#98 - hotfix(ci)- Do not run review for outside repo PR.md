# #98 hotfix(ci): Do not run review for outside repo PR [Merged]

> Username: coder3101  
> Created at: 2020-08-04 09:57:03 UTC  
> Updated at: 2020-08-04 20:17:13 UTC  
> Merged at: 2020-08-04 20:17:13 UTC  
> Closed at: 2020-08-04 20:17:13 UTC  
> Url: https://github.com/boostorg/ublas/pull/98  

The outside repositories do not have permission to post review comments on ublas repository. It fails to review and CI fails. This issue has been fixed. Now this action is only run if PR is originating from our own repository.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-08-04 10:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/98#issuecomment-668507992  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/98?src=pr&el=h1) Report  
> Merging [#98](https://codecov.io/gh/boostorg/ublas/pull/98?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/7cc02bf0a10ad1820db83e5d4ed19b0510da2f1e&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/98/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/98?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #98   +/-   ##  
========================================  
  Coverage    95.33%   95.33%             
========================================  
  Files           22       22             
  Lines         1223     1223             
========================================  
  Hits          1166     1166             
  Misses          57       57             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/98?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/98?src=pr&el=footer). Last update [7cc02bf...ba4053f](https://codecov.io/gh/boostorg/ublas/pull/98?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
