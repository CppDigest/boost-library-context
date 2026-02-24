# #209 max_depth is specified with parse_options [Closed]

> Username: sdkrystian  
> Created at: 2020-08-25 17:13:36 UTC  
> Updated at: 2020-08-27 02:41:24 UTC  
> Closed at: 2020-08-27 02:41:24 UTC  
> Url: https://github.com/boostorg/json/pull/209  

fix #205

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-25 17:32:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/209#pullrequestreview-474705146  

📁 include/boost/json/detail/basic_parser.hpp

```diff
 198 |     parse_options opt_;
 199 |+     // how many levels deeper the parser can go
 200 |+     std::size_t depth_ = opt_.max_depth;
```

> Username: vinniefalco  
> Created_at: 2020-08-25 17:32:28 UTC  
> Url: https://github.com/boostorg/json/pull/209#discussion_r476621639  

could call it `depth_remain_`. or not.

> Username: sdkrystian  
> Created_at: 2020-08-25 20:19:10 UTC  
> Updated_at: 2020-08-25 20:19:11 UTC  
> Url: https://github.com/boostorg/json/pull/209#discussion_r476712816  

Meh, not too important. If you want to rename it, go ahead.

> Username: vinniefalco  
> Created_at: 2020-08-25 21:05:19 UTC  
> Url: https://github.com/boostorg/json/pull/209#discussion_r476736693  

I don't feel strongly about it


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-08-26 14:13:20 UTC  
> Updated_at: 2020-08-26 16:13:47 UTC  
> Url: https://github.com/boostorg/json/pull/209#issuecomment-680905837  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/209?src=pr&el=h1) Report  
> Merging [#209](https://codecov.io/gh/CPPAlliance/json/pull/209?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/523332ae81a01ccb26a3df1c087f9b58fcdde3de?el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/209/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/209?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #209      +/-   ##  
===========================================  
- Coverage    97.93%   97.92%   -0.01%       
===========================================  
  Files           65       65                
  Lines         5654     5645       -9       
===========================================  
- Hits          5537     5528       -9       
  Misses         117      117                
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/209?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/209/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/209/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `99.65% <100.00%> (ø)` | |  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/209/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/209?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/209?src=pr&el=footer). Last update [523332a...34b5713](https://codecov.io/gh/CPPAlliance/json/pull/209?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
