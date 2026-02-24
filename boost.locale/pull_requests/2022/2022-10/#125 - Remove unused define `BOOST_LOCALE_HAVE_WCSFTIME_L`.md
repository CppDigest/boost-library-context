# #125 Remove unused define `BOOST_LOCALE_HAVE_WCSFTIME_L` [Merged]

> Username: Flamefire  
> Created at: 2022-10-22 12:26:40 UTC  
> Updated at: 2022-10-24 17:55:31 UTC  
> Merged at: 2022-10-23 12:09:42 UTC  
> Closed at: 2022-10-23 12:09:42 UTC  
> Url: https://github.com/boostorg/locale/pull/125  

Also remove the code guarded by `HAVE_WCSFTIME_L` which was never used. The fallback seems to be good enough.  
Closes #88

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-22 18:46:11 UTC  
> Url: https://github.com/boostorg/locale/pull/125#issuecomment-1287880717  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/125?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#125](https://codecov.io/gh/boostorg/locale/pull/125?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fc06856) into [develop](https://codecov.io/gh/boostorg/locale/commit/cc0337c6a93d2637d8157269a59912f68f8db3b4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cc0337c) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/125/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/125?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #125   +/-   ##  
========================================  
  Coverage    88.45%   88.45%             
========================================  
  Files          100      100             
  Lines         8977     8977             
========================================  
  Hits          7941     7941             
  Misses        1036     1036             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/125?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/posix/numeric.cpp](https://codecov.io/gh/boostorg/locale/pull/125/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9udW1lcmljLmNwcA==) | `69.12% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/125?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/125?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cc0337c...fc06856](https://codecov.io/gh/boostorg/locale/pull/125?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
