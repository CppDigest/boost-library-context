# #345 fix pip install in drone script [Closed]

> Username: alandefreitas  
> Created at: 2022-08-05 17:46:54 UTC  
> Updated at: 2022-08-08 14:38:34 UTC  
> Closed at: 2022-08-05 21:47:06 UTC  
> Url: https://github.com/boostorg/url/pull/345  

With this PR, we fix all warnings we could have in CI.   
  
The only warning we are explicitly ignoring after that is `unused-function`. Since it's a problem with GCC6-8 that's already been fixed in GCC, there's nothing we can do about it. At most, we could include a link to the bug.  
  
In the end, all we needed to fix the script was to upgrade pip. The cmake subdir test was also simplified to make errors there much less likely.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-05 18:05:04 UTC  
> Updated_at: 2022-08-05 21:11:59 UTC  
> Url: https://github.com/boostorg/url/pull/345#issuecomment-1206713285  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/345?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#345](https://codecov.io/gh/CPPAlliance/url/pull/345?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (3c0f016) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/c1a0f7c3b35ba0de750c1ae25928dc68ab98d9b2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (c1a0f7c) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/345/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/345?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #345   +/-   ##  
========================================  
  Coverage    96.16%   96.16%             
========================================  
  Files          124      124             
  Lines         6161     6161             
========================================  
  Hits          5925     5925             
  Misses         236      236             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/345?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/345/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/CPPAlliance/url/pull/345/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/345?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/345?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [c1a0f7c...3c0f016](https://codecov.io/gh/CPPAlliance/url/pull/345?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2022-08-05 18:15:55 UTC  
> Url: https://github.com/boostorg/url/pull/345#issuecomment-1206721443  

GHA caught an error that Drone didn't catch. I'll fix it.

---
