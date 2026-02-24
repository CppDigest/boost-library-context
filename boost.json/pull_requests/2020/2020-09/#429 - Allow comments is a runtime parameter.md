# #429 Allow comments is a runtime parameter [Closed]

> Username: sdkrystian  
> Created at: 2020-09-28 16:41:53 UTC  
> Updated at: 2020-10-04 19:39:29 UTC  
> Closed at: 2020-10-04 19:39:28 UTC  
> Url: https://github.com/boostorg/json/pull/429  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-09-28 16:56:45 UTC  
> Url: https://github.com/boostorg/json/pull/429#issuecomment-700158135  

needs work

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-28 17:31:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/429#pullrequestreview-497751100  

📁 include/boost/json/basic_parser.hpp

```diff
 689 |+         if(BOOST_JSON_UNLIKELY(p == end_))
 690 |+             return maybe_suspend(p, state::val1);
 691 |+         return parse_value(p, std::true_type(), allow_comments, allow_trailing, allow_bad_utf8);
```

> Username: vinniefalco  
> Created_at: 2020-09-28 17:31:57 UTC  
> Url: https://github.com/boostorg/json/pull/429#discussion_r496119278  

`parse_value` calls `resume_value` which calls `parse_value` this is not right


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-09-28 19:22:32 UTC  
> Updated_at: 2020-09-28 19:22:35 UTC  
> Url: https://github.com/boostorg/json/pull/429#issuecomment-700232338  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/429?src=pr&el=h1) Report  
> Merging [#429](https://codecov.io/gh/CPPAlliance/json/pull/429?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/08dd10b5f54a536d94751d4758054a2480597020?el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `95.55%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/429/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/429?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #429      +/-   ##  
===========================================  
- Coverage    98.42%   98.40%   -0.02%       
===========================================  
  Files           71       71                
  Lines         5953     5957       +4       
===========================================  
+ Hits          5859     5862       +3       
- Misses          94       95       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/429?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/429/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/429/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `99.18% <95.55%> (-0.08%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/429?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/429?src=pr&el=footer). Last update [08dd10b...db80dba](https://codecov.io/gh/CPPAlliance/json/pull/429?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-10-04 19:39:28 UTC  
> Url: https://github.com/boostorg/json/pull/429#issuecomment-703305320  

Merged, thanks

---
