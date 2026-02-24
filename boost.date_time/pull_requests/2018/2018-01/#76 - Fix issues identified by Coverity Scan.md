# #76 Fix issues identified by Coverity Scan [Merged]

> Username: jeking3  
> Created at: 2018-01-23 16:17:49 UTC  
> Updated at: 2018-01-23 22:57:37 UTC  
> Merged at: 2018-01-23 22:57:37 UTC  
> Closed at: 2018-01-23 22:57:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/76  

https://scan.coverity.com/projects/boostorg-date_time?tab=overview  
  
This resolves all 22 open issues.

---

## Review 1 [Approved]

> Username: pdimov  
> Created_at: 2018-01-23 16:36:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/date_time/pull/76#pullrequestreview-90892859  

Looks good.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-01-23 21:06:30 UTC  
> Url: https://github.com/boostorg/date_time/pull/76#issuecomment-359930460  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/76?src=pr&el=h1) Report  
> Merging [#76](https://codecov.io/gh/boostorg/date_time/pull/76?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/8b92513a947e6311854ac6d3b9519f175c93017d?src=pr&el=desc) will **increase** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/76/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g)](https://codecov.io/gh/boostorg/date_time/pull/76?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #76      +/-   ##  
===========================================  
+ Coverage    93.76%   93.78%   +0.02%       
===========================================  
  Files           80       80                
  Lines         4874     5020     +146       
===========================================  
+ Hits          4570     4708     +138       
- Misses         304      312       +8  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/76?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/local\_time\_adjustor.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvbG9jYWxfdGltZV9hZGp1c3Rvci5ocHA=) | | |  
| [include/boost/date\_time/dst\_rules.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZHN0X3J1bGVzLmhwcA==) | | |  
| [include/boost/date\_time/gregorian/greg\_day.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfZGF5LmhwcA==) | | |  
| [include/boost/date\_time/time.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZS5ocHA=) | | |  
| [include/boost/date\_time/date\_generator\_parser.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZGF0ZV9nZW5lcmF0b3JfcGFyc2VyLmhwcA==) | | |  
| [...boost/date\_time/posix\_time/posix\_time\_duration.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS9wb3NpeF90aW1lX2R1cmF0aW9uLmhwcA==) | | |  
| [...clude/boost/date\_time/gregorian/greg\_serialize.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfc2VyaWFsaXplLmhwcA==) | | |  
| [...clude/boost/date\_time/posix\_time/posix\_time\_io.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS9wb3NpeF90aW1lX2lvLmhwcA==) | | |  
| [include/boost/date\_time/time\_iterator.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9pdGVyYXRvci5ocHA=) | | |  
| [include/boost/date\_time/posix\_time/conversion.hpp](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS9jb252ZXJzaW9uLmhwcA==) | | |  
| ... and [141 more](https://codecov.io/gh/boostorg/date_time/pull/76/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/76?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/76?src=pr&el=footer). Last update [8b92513...0a153d4](https://codecov.io/gh/boostorg/date_time/pull/76?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
