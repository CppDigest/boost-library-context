# #169 remove unnecessary include [Merged]

> Username: alandefreitas  
> Created at: 2022-05-03 21:17:59 UTC  
> Updated at: 2022-05-04 14:44:37 UTC  
> Merged at: 2022-05-04 14:44:27 UTC  
> Closed at: 2022-05-04 14:44:27 UTC  
> Url: https://github.com/boostorg/url/pull/169  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-03 23:12:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/169#pullrequestreview-961177347  

📁 include/boost/url/detail/except.hpp

```diff
  13 | #include <boost/url/error.hpp>
  14 | #include <boost/assert/source_location.hpp>
  15 |- #include <boost/exception/diagnostic_information.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-05-03 23:12:22 UTC  
> Updated_at: 2022-05-03 23:12:23 UTC  
> Url: https://github.com/boostorg/url/pull/169#discussion_r864324104  

excellent


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-05-04 00:34:54 UTC  
> Url: https://github.com/boostorg/url/pull/169#issuecomment-1116820469  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/169?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#169](https://codecov.io/gh/CPPAlliance/url/pull/169?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (c9acc24) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/1a9b2f9faa8fa212c8088fcf15e96d0938aab5aa?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (1a9b2f9) will **decrease** coverage by `0.04%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/169/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #169      +/-   ##  
===========================================  
- Coverage    96.76%   96.72%   -0.05%       
===========================================  
  Files          114      119       +5       
  Lines         6129     6047      -82       
===========================================  
- Hits          5931     5849      -82       
  Misses         198      198                
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/params.ipp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaXBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/query\_param.hpp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcXVlcnlfcGFyYW0uaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfdmlldy5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/params\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfdmlldy5pcHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/const\_string.ipp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9jb25zdF9zdHJpbmcuaXBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/params\_encoded.hpp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/params\_encoded.ipp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZC5pcHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/params\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZF92aWV3LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| ... and [11 more](https://codecov.io/gh/CPPAlliance/url/pull/169/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/169?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/169?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [1a9b2f9...c9acc24](https://codecov.io/gh/CPPAlliance/url/pull/169?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-05-04 05:44:33 UTC  
> Url: https://github.com/boostorg/url/pull/169#issuecomment-1116950059  

This is good to go please merge it

---
