# #552 authority view string token [Closed]

> Username: alandefreitas  
> Created at: 2022-09-15 16:50:28 UTC  
> Updated at: 2022-09-19 21:26:25 UTC  
> Closed at: 2022-09-15 19:48:57 UTC  
> Url: https://github.com/boostorg/url/pull/552  

fix #546

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-15 16:58:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/552#pullrequestreview-1109559522  

📁 include/boost/url/impl/authority_view.ipp

```diff
 106 | }
 107 | 
 117 |- std::string
```

> Username: vinniefalco  
> Created_at: 2022-09-15 16:58:30 UTC  
> Updated_at: 2022-09-15 16:58:31 UTC  
> Url: https://github.com/boostorg/url/pull/552#discussion_r972230519  

is there any way for us to know the decoded size of the userinfo?

> Username: alandefreitas  
> Created_at: 2022-09-15 19:02:07 UTC  
> Url: https://github.com/boostorg/url/pull/552#discussion_r972331865  

Updated.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-09-15 17:09:54 UTC  
> Updated_at: 2022-09-15 19:18:25 UTC  
> Url: https://github.com/boostorg/url/pull/552#issuecomment-1248381111  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/552?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#552](https://codecov.io/gh/boostorg/url/pull/552?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (be92484) into [develop](https://codecov.io/gh/boostorg/url/commit/49112f4a0d1705799c17ef8b622ac42f51efdafa?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (49112f4) will **increase** coverage by `0.03%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/552/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/552?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #552      +/-   ##  
===========================================  
+ Coverage    96.70%   96.74%   +0.03%       
===========================================  
  Files          137      137                
  Lines         6618     6601      -17       
===========================================  
- Hits          6400     6386      -14       
+ Misses         218      215       -3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/552?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `93.93% <100.00%> (+9.32%)` | :arrow_up: |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `78.86% <100.00%> (+1.99%)` | :arrow_up: |  
| [include/boost/url/impl/url\_view\_base.ipp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlld19iYXNlLmlwcA==) | `98.59% <100.00%> (ø)` | |  
| [include/boost/url/impl/static\_url.ipp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zdGF0aWNfdXJsLmlwcA==) | `70.83% <0.00%> (-20.84%)` | :arrow_down: |  
| [include/boost/url/static\_url.hpp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvc3RhdGljX3VybC5ocHA=) | `80.00% <0.00%> (-20.00%)` | :arrow_down: |  
| [include/boost/url/url.hpp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/ipv4\_address.ipp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9pcHY0X2FkZHJlc3MuaXBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/impl/ipv6\_address.ipp](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9pcHY2X2FkZHJlc3MuaXBw) | `100.00% <0.00%> (ø)` | |  
| ... and [3 more](https://codecov.io/gh/boostorg/url/pull/552/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/552?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/552?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [49112f4...be92484](https://codecov.io/gh/boostorg/url/pull/552?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
