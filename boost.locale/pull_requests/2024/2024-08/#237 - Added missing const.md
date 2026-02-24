# #237 Added missing const [Merged]

> Username: BrianWeed  
> Created at: 2024-08-15 17:06:51 UTC  
> Updated at: 2024-08-16 00:45:20 UTC  
> Merged at: 2024-08-15 23:34:43 UTC  
> Closed at: 2024-08-15 23:34:43 UTC  
> Url: https://github.com/boostorg/locale/pull/237  

empty() is a simple read-only getter, and therefore should be const.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-15 23:34:46 UTC  
> Updated_at: 2024-08-16 00:45:20 UTC  
> Url: https://github.com/boostorg/locale/pull/237#issuecomment-2292456485  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/237?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.76%. Comparing base [(`ea81c22`)](https://app.codecov.io/gh/boostorg/locale/commit/ea81c226cf315046e2d58c95de2ce7c19db4d578?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`83748ff`)](https://app.codecov.io/gh/boostorg/locale/commit/83748ff1814cd824ddad92be303a344593e7a6d3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/237/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/237?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #237   +/-   ##  
========================================  
  Coverage    95.76%   95.76%             
========================================  
  Files          116      116             
  Lines        10035    10035             
========================================  
  Hits          9610     9610             
  Misses         425      425             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/locale/pull/237?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/shared/message.cpp](https://app.codecov.io/gh/boostorg/locale/pull/237?src=pr&el=tree&filepath=src%2Fboost%2Flocale%2Fshared%2Fmessage.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbWVzc2FnZS5jcHA=) | `97.02% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/237?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/237?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ea81c22...83748ff](https://app.codecov.io/gh/boostorg/locale/pull/237?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
