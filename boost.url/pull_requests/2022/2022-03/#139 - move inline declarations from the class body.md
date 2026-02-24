# #139 move inline declarations from the class body [Closed]

> Username: alandefreitas  
> Created at: 2022-03-15 00:07:56 UTC  
> Updated at: 2022-03-16 15:32:28 UTC  
> Closed at: 2022-03-15 18:38:40 UTC  
> Url: https://github.com/boostorg/url/pull/139  

fix #109  
  
This fixes all declarations and definitions at once.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-03-15 03:09:06 UTC  
> Url: https://github.com/boostorg/url/pull/139#issuecomment-1067515643  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/139?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#139](https://codecov.io/gh/CPPAlliance/url/pull/139?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (7f0715e) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/fc070a5b7369644f8d567e034d1bda72fb259f17?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (fc070a5) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/139/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/139?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #139   +/-   ##  
========================================  
  Coverage    96.63%   96.63%             
========================================  
  Files          109      109             
  Lines         6071     6071             
========================================  
  Hits          5867     5867             
  Misses         204      204             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/139?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaHBw) | `99.37% <ø> (ø)` | |  
| [include/boost/url/impl/params\_encoded.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZC5ocHA=) | `99.35% <ø> (ø)` | |  
| [include/boost/url/impl/params\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZF92aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50cy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments\_encoded.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c19lbmNvZGVkLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c19lbmNvZGVkX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| ... and [7 more](https://codecov.io/gh/CPPAlliance/url/pull/139/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/139?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/139?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [fc070a5...7f0715e](https://codecov.io/gh/CPPAlliance/url/pull/139?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---
