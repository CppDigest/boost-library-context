# #588 Remove unnecessary trailing characters [Merged]

> Username: grisumbras  
> Created at: 2021-06-26 13:24:12 UTC  
> Updated at: 2021-06-26 17:56:55 UTC  
> Merged at: 2021-06-26 17:56:52 UTC  
> Closed at: 2021-06-26 17:56:52 UTC  
> Url: https://github.com/boostorg/json/pull/588  

Removes unnecessary semicolons that result in warnings when pedantic warnings are enabled.  
Also removes trailing whitespace.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-06-26 13:31:15 UTC  
> Url: https://github.com/boostorg/json/pull/588#issuecomment-869002411  

An automated preview of the documentation is available at [https://588.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://588.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-06-26 13:47:21 UTC  
> Url: https://github.com/boostorg/json/pull/588#issuecomment-869005573  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/588?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#588](https://codecov.io/gh/boostorg/json/pull/588?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (445efad) into [develop](https://codecov.io/gh/boostorg/json/commit/351603c9b2dee7a80c8433841b50f7294131b25a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (351603c) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/588/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/588?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #588      +/-   ##  
===========================================  
- Coverage    99.05%   99.05%   -0.01%       
===========================================  
  Files           68       68                
  Lines         6115     6114       -1       
===========================================  
- Hits          6057     6056       -1       
  Misses          58       58                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/588?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/array.hpp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9hcnJheS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/config.hpp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/default\_resource.hpp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9kZWZhdWx0X3Jlc291cmNlLmhwcA==) | `75.00% <ø> (ø)` | |  
| [include/boost/json/detail/hash\_combine.hpp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9oYXNoX2NvbWJpbmUuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/shared\_resource.hpp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zaGFyZWRfcmVzb3VyY2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/stream.hpp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdHJlYW0uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/utf8.hpp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC91dGY4LmhwcA==) | `95.00% <ø> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <ø> (ø)` | |  
| ... and [14 more](https://codecov.io/gh/boostorg/json/pull/588/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/588?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/588?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [351603c...445efad](https://codecov.io/gh/boostorg/json/pull/588?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-06-26 14:39:06 UTC  
> Url: https://github.com/boostorg/json/pull/588#issuecomment-869012351  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/588/pullrequest-condensed-cec3967.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/588/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/588/pullrequest.html)

---
