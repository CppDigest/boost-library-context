# #64 Fix value_ref construction for const rvalues [Closed]

> Username: sdkrystian  
> Created at: 2020-03-27 19:51:27 UTC  
> Updated at: 2020-06-10 15:20:28 UTC  
> Closed at: 2020-04-13 19:27:43 UTC  
> Url: https://github.com/boostorg/json/pull/64  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-27 20:02:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/64#pullrequestreview-383164074  

📁 test/value_ref.cpp

```diff
  95 |+ 
  96 |+         // test that value_ref can be constructed
  97 |+         // from a const rvalue
```

> Username: vinniefalco  
> Created_at: 2020-03-27 20:02:01 UTC  
> Updated_at: 2020-03-27 20:17:19 UTC  
> Url: https://github.com/boostorg/json/pull/64#discussion_r399509626  

This comment can be terse:  
```  
// construct from const rvalue  
```  
  
The rest is implied by context.

> Username: sdkrystian  
> Created_at: 2020-03-27 20:17:48 UTC  
> Updated_at: 2020-03-27 20:17:49 UTC  
> Url: https://github.com/boostorg/json/pull/64#discussion_r399516593  

Alright, good to go.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-03-27 20:32:34 UTC  
> Updated_at: 2020-03-27 23:43:02 UTC  
> Url: https://github.com/boostorg/json/pull/64#issuecomment-605302424  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/64?src=pr&el=h1) Report  
> Merging [#64](https://codecov.io/gh/CPPAlliance/json/pull/64?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/a7ef611630c9ca532a4ebfda062240e86d09ba67&el=desc) will **decrease** coverage by `0.08%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/64/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/64?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #64      +/-   ##  
===========================================  
- Coverage    99.28%   99.19%   -0.09%       
===========================================  
  Files           58       57       -1       
  Lines         5042     5229     +187       
===========================================  
+ Hits          5006     5187     +181       
- Misses          36       42       +6       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/64?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value\_ref.hpp](https://codecov.io/gh/CPPAlliance/json/pull/64/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3JlZi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/serializer.ipp](https://codecov.io/gh/CPPAlliance/json/pull/64/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5pcHA=) | `98.57% <0.00%> (-1.43%)` | :arrow_down: |  
| [include/boost/json/impl/basic\_parser.ipp](https://codecov.io/gh/CPPAlliance/json/pull/64/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `99.55% <0.00%> (-0.45%)` | :arrow_down: |  
| [include/boost/json/serializer.hpp](https://codecov.io/gh/CPPAlliance/json/pull/64/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3NlcmlhbGl6ZXIuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/sse2.hpp](https://codecov.io/gh/CPPAlliance/json/pull/64/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zc2UyLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/config.hpp](https://codecov.io/gh/CPPAlliance/json/pull/64/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/stream.hpp](https://codecov.io/gh/CPPAlliance/json/pull/64/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdHJlYW0uaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/static\_stack.hpp](https://codecov.io/gh/CPPAlliance/json/pull/64/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdGF0aWNfc3RhY2suaHBw) | | |  
| [include/boost/json/detail/impl/stack.ipp](https://codecov.io/gh/CPPAlliance/json/pull/64/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0YWNrLmlwcA==) | `100.00% <0.00%> (+12.50%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/64?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/64?src=pr&el=footer). Last update [a7ef611...4946259](https://codecov.io/gh/CPPAlliance/json/pull/64?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: sdkrystian  
> Created_at: 2020-04-13 19:27:43 UTC  
> Url: https://github.com/boostorg/json/pull/64#issuecomment-613055231  

Fixed by https://github.com/CPPAlliance/json/commit/fdee3ceb71a04c3b5f1b548a9dda9ae8ebe04b4d

---
