# #835 default source_location argument for throwing functions [Merged]

> Username: grisumbras  
> Created at: 2023-01-09 13:16:02 UTC  
> Updated at: 2023-01-09 14:29:44 UTC  
> Merged at: 2023-01-09 13:57:06 UTC  
> Closed at: 2023-01-09 13:57:07 UTC  
> Url: https://github.com/boostorg/json/pull/835  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-01-09 13:32:30 UTC  
> Updated_at: 2023-01-09 13:33:18 UTC  
> Url: https://github.com/boostorg/json/pull/835#issuecomment-1375632486  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#835](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (607b21d) into [develop](https://codecov.io/gh/boostorg/json/commit/36552f0c94e4b06cdb697c6ee186a5bc33724314?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (36552f0) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/835/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #835      +/-   ##  
===========================================  
- Coverage    99.01%   99.00%   -0.01%       
===========================================  
  Files           70       70                
  Lines         6883     6837      -46       
===========================================  
- Hits          6815     6769      -46       
  Misses          68       68                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/string\_impl.ipp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0cmluZ19pbXBsLmlwcA==) | `99.11% <100.00%> (-0.04%)` | :arrow_down: |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/array.hpp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/null\_resource.ipp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvbnVsbF9yZXNvdXJjZS5pcHA=) | `80.00% <100.00%> (-1.82%)` | :arrow_down: |  
| [include/boost/json/impl/object.hpp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/parse.ipp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2UuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/pointer.ipp](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcG9pbnRlci5pcHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [5 more](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [36552f0...607b21d](https://codecov.io/gh/boostorg/json/pull/835?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-01-09 14:29:43 UTC  
> Url: https://github.com/boostorg/json/pull/835#issuecomment-1375708356  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/835/pullrequest-condensed-dc80adb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/835/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/835/pullrequest.html)

---
