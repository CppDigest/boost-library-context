# #354 Fix standalone compilation with gcc8.3 [Closed]

> Username: Julien-Blanc-tgcm  
> Created at: 2020-09-17 11:29:39 UTC  
> Updated at: 2020-09-17 20:57:59 UTC  
> Closed at: 2020-09-17 20:57:59 UTC  
> Url: https://github.com/boostorg/json/pull/354  

gcc 8.3 does not ship with the memory_resource header, but ships with  
experimental/memory_resource  
  
This header is required to use json without boost.  
  
Since gcc 8.3 is what’s include in debian stable, it can be expected  
that it will benefit many users to fallback on  
experimental/memory_resource if possible.  
  
Signed-off-by: Julien Blanc <julien.blanc@tgcm.eu>

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-09-17 12:36:24 UTC  
> Url: https://github.com/boostorg/json/pull/354#issuecomment-694201286  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/354/pullrequest-condensed-76130f3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/354/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/354/pullrequest.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-09-17 13:48:27 UTC  
> Updated_at: 2020-09-17 13:48:30 UTC  
> Url: https://github.com/boostorg/json/pull/354#issuecomment-694247606  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/354?src=pr&el=h1) Report  
> Merging [#354](https://codecov.io/gh/CPPAlliance/json/pull/354?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/2d0bef633d47418461710639fda58f6db68f6def?el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/354/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/354?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #354      +/-   ##  
===========================================  
- Coverage    97.85%   97.84%   -0.02%       
===========================================  
  Files           69       68       -1       
  Lines         5774     5793      +19       
===========================================  
+ Hits          5650     5668      +18       
- Misses         124      125       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/354?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/null\_resource.ipp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvbnVsbF9yZXNvdXJjZS5pcHA=) | `81.81% <0.00%> (-18.19%)` | :arrow_down: |  
| [include/boost/json/impl/static\_resource.ipp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RhdGljX3Jlc291cmNlLmlwcA==) | `81.81% <0.00%> (-2.19%)` | :arrow_down: |  
| [include/boost/json/array.hpp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/impl/array.hpp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaHBw) | `96.82% <0.00%> (ø)` | |  
| [include/boost/json/serializer.hpp](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3NlcmlhbGl6ZXIuaHBw) | `100.00% <0.00%> (ø)` | |  
| ... and [7 more](https://codecov.io/gh/CPPAlliance/json/pull/354/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/354?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/354?src=pr&el=footer). Last update [2d0bef6...2da8a34](https://codecov.io/gh/CPPAlliance/json/pull/354?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
