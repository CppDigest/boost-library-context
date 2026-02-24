# #636 Add noexcept to destructors to support /Zc:implicitNoexcept-. Fixes #557. [Merged]

> Username: pdimov  
> Created at: 2021-10-08 00:20:26 UTC  
> Updated at: 2021-10-08 20:43:20 UTC  
> Merged at: 2021-10-08 20:42:21 UTC  
> Closed at: 2021-10-08 20:42:21 UTC  
> Url: https://github.com/boostorg/json/pull/636  

Fix #557

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-10-08 00:36:38 UTC  
> Updated_at: 2021-10-08 00:45:31 UTC  
> Url: https://github.com/boostorg/json/pull/636#issuecomment-938249592  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/636?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#636](https://codecov.io/gh/boostorg/json/pull/636?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d085e86) into [develop](https://codecov.io/gh/boostorg/json/commit/9af467c41631d5b4bf3810a154b7d838bac2fef4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9af467c) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/636/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/636?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #636   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           68       68             
  Lines         6119     6119             
========================================  
  Hits          6061     6061             
  Misses          58       58             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/636?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://codecov.io/gh/boostorg/json/pull/636/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/636/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://codecov.io/gh/boostorg/json/pull/636/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/boostorg/json/pull/636/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/value.ipp](https://codecov.io/gh/boostorg/json/pull/636/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/storage\_ptr.hpp](https://codecov.io/gh/boostorg/json/pull/636/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0b3JhZ2VfcHRyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/boostorg/json/pull/636/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/636/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.98% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/636?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/636?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9af467c...d085e86](https://codecov.io/gh/boostorg/json/pull/636?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-10-08 01:34:01 UTC  
> Url: https://github.com/boostorg/json/pull/636#issuecomment-938269901  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/636/pullrequest-condensed-08b6f5f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/636/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/636/pullrequest.html)

---
