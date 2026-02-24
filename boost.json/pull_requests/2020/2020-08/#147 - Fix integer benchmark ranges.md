# #147 Fix integer benchmark ranges [Closed]

> Username: sdkrystian  
> Created at: 2020-08-10 23:50:54 UTC  
> Updated at: 2020-09-01 18:56:19 UTC  
> Closed at: 2020-09-01 18:56:19 UTC  
> Url: https://github.com/boostorg/json/pull/147  

`integers-32` contains numbers in the range `-2^32 to 2^32 - 1` and `integers-64` contains numbers in the range `-2^64 to 2^64 - 1` that don't fall within the range of `integers-32`. Numbers are generated randomly based on length, so it's a good mix of short and long numbers.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-08-12 01:48:38 UTC  
> Updated_at: 2020-08-24 22:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/147#issuecomment-672446959  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/147?src=pr&el=h1) Report  
> Merging [#147](https://codecov.io/gh/CPPAlliance/json/pull/147?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/ec29dfa768a16672534f758005dfe4841a8ea93d?el=desc) will **increase** coverage by `0.08%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/147/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/147?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #147      +/-   ##  
===========================================  
+ Coverage    98.34%   98.43%   +0.08%       
===========================================  
  Files           63       64       +1       
  Lines         5754     7155    +1401       
===========================================  
+ Hits          5659     7043    +1384       
- Misses          95      112      +17       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/147?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/sse2.hpp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zc2UyLmhwcA==) | `99.02% <0.00%> (-0.98%)` | :arrow_down: |  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `99.17% <0.00%> (-0.83%)` | :arrow_down: |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `99.90% <0.00%> (-0.10%)` | :arrow_down: |  
| [include/boost/json/array.hpp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value\_ref.hpp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3JlZi5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/impl/error.ipp](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
| ... and [12 more](https://codecov.io/gh/CPPAlliance/json/pull/147/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/147?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/147?src=pr&el=footer). Last update [ec29dfa...d232b36](https://codecov.io/gh/CPPAlliance/json/pull/147?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: sdkrystian  
> Created_at: 2020-08-16 17:20:10 UTC  
> Updated_at: 2020-08-16 17:20:23 UTC  
> Url: https://github.com/boostorg/json/pull/147#issuecomment-674553163  

@vinniefalco Updated to include `-2^32 to 2^32 - 1` in `integers-64`

---

## Comment 3

> Username: sdkrystian  
> Created_at: 2020-08-24 15:11:16 UTC  
> Url: https://github.com/boostorg/json/pull/147#issuecomment-679186687  

Whoops, accidently deleted the branch when pruning stale branches.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-09-01 15:16:40 UTC  
> Url: https://github.com/boostorg/json/pull/147#issuecomment-684931012  

Is this relevant now that we are using an updated set of benchmark files?

---

## Comment 5

> Username: sdkrystian  
> Created_at: 2020-09-01 18:56:19 UTC  
> Url: https://github.com/boostorg/json/pull/147#issuecomment-685068087  

Nope, this can be closed.

---
