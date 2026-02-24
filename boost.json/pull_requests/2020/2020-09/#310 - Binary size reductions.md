# #310 Binary size reductions [Closed]

> Username: sdkrystian  
> Created at: 2020-09-10 17:14:52 UTC  
> Updated at: 2020-09-20 21:52:21 UTC  
> Closed at: 2020-09-20 21:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/310  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-10 17:21:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/310#pullrequestreview-486151367  

📁 include/boost/json/detail/config.hpp

```diff
 207 |+ #  define BOOST_JSON_UNREACHABLE() do { } while(1)
 208 |+ # endif
 209 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2020-09-10 17:21:22 UTC  
> Updated_at: 2020-09-16 19:22:04 UTC  
> Url: https://github.com/boostorg/json/pull/310#discussion_r486509210  

@pdimov is this correct?

> Username: pdimov  
> Created_at: 2020-09-10 22:10:03 UTC  
> Updated_at: 2020-09-16 19:22:04 UTC  
> Url: https://github.com/boostorg/json/pull/310#discussion_r486661359  

The [[noreturn]] fallback is probably better than an infinite loop.


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-09-10 18:06:23 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-690589230  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest-condensed-dc3928b.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-09-10 18:51:47 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-690618669  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest-condensed-b9a086f.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2020-09-11 00:46:22 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-690806507  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest-condensed-b42b475.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-09-11 01:46:46 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-690823950  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest-condensed-40ee309.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html)

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2020-09-15 02:08:17 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-692417515  

I think we should just have the commit with the `parse_comment` trimmed down, and worry about getting the unreachable() thing right later. This would give an immediate savings, with the promise of more down the line.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2020-09-16 18:31:13 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-693583874  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest-condensed-4eaaddf.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2020-09-16 19:31:37 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-693620232  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest-condensed-1af4251.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2020-09-16 20:31:57 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-693649189  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest-condensed-6a2eff6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/310/pullrequest.html)

---

## Comment 10

> Username: codecov[bot]  
> Created_at: 2020-09-16 23:21:38 UTC  
> Updated_at: 2020-09-16 23:21:40 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-693717503  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/310?src=pr&el=h1) Report  
> Merging [#310](https://codecov.io/gh/CPPAlliance/json/pull/310?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/07214ad235b0ea43a1ef49a11ee3807d8198cebe?el=desc) will **increase** coverage by `0.31%`.  
> The diff coverage is `96.82%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/310/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/310?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #310      +/-   ##  
===========================================  
+ Coverage    97.52%   97.84%   +0.31%       
===========================================  
  Files           69       69                
  Lines         5689     5791     +102       
===========================================  
+ Hits          5548     5666     +118       
+ Misses         141      125      -16       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/310?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/config.hpp](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `98.81% <96.82%> (-0.25%)` | :arrow_down: |  
| [include/boost/json/impl/monotonic\_resource.ipp](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvbW9ub3RvbmljX3Jlc291cmNlLmlwcA==) | `97.01% <0.00%> (-2.99%)` | :arrow_down: |  
| [include/boost/json/impl/value.ipp](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaXBw) | `97.11% <0.00%> (-2.89%)` | :arrow_down: |  
| [include/boost/json/impl/static\_resource.ipp](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RhdGljX3Jlc291cmNlLmlwcA==) | `81.81% <0.00%> (-2.19%)` | :arrow_down: |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `98.09% <0.00%> (-0.25%)` | :arrow_down: |  
| [include/boost/json/array.hpp](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| ... and [20 more](https://codecov.io/gh/CPPAlliance/json/pull/310/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/310?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/310?src=pr&el=footer). Last update [07214ad...a7996c0](https://codecov.io/gh/CPPAlliance/json/pull/310?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2020-09-20 21:52:20 UTC  
> Url: https://github.com/boostorg/json/pull/310#issuecomment-695840966  

Merged

---
