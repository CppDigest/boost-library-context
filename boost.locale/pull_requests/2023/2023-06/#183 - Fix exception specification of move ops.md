# #183 Fix exception specification of move ops [Merged]

> Username: Flamefire  
> Created at: 2023-06-20 08:46:46 UTC  
> Updated at: 2023-06-21 07:40:18 UTC  
> Merged at: 2023-06-21 07:40:15 UTC  
> Closed at: 2023-06-21 07:40:15 UTC  
> Url: https://github.com/boostorg/locale/pull/183  

This fixes some warnings and/or errors especially using Clang.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-20 21:12:27 UTC  
> Url: https://github.com/boostorg/locale/pull/183#issuecomment-1599544347  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#183](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (233efe5) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/706b23cfa3550c2016f4f32ca6995daecbadd6ce?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (706b23c) will **increase** coverage by `16.96%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/183/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff              @@  
##           develop     #183       +/-   ##  
============================================  
+ Coverage    75.73%   92.69%   +16.96%       
============================================  
  Files           90      112       +22       
  Lines         6902     9817     +2915       
============================================  
+ Hits          5227     9100     +3873       
+ Misses        1675      717      -958       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/date\_time.hpp](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lLmhwcA==) | `94.02% <100.00%> (+4.25%)` | :arrow_up: |  
| [include/boost/locale/format.hpp](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0LmhwcA==) | `94.32% <100.00%> (+94.32%)` | :arrow_up: |  
| [include/boost/locale/message.hpp](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `100.00% <100.00%> (+0.97%)` | :arrow_up: |  
  
... and [101 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/183/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [706b23c...233efe5](https://app.codecov.io/gh/boostorg/locale/pull/183?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
