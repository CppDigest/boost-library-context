# #56 Work around a gcc-7.3 issue running ubsan on travis CI [Merged]

> Username: jeking3  
> Created at: 2018-04-29 12:23:07 UTC  
> Updated at: 2018-04-29 13:34:30 UTC  
> Merged at: 2018-04-29 13:31:56 UTC  
> Closed at: 2018-04-29 13:31:56 UTC  
> Url: https://github.com/boostorg/format/pull/56  

https://stackoverflow.com/questions/50024731/ld-unrecognized-option-push-state-no-as-needed

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-04-29 13:27:24 UTC  
> Updated_at: 2018-04-29 13:28:52 UTC  
> Url: https://github.com/boostorg/format/pull/56#issuecomment-385251492  

# [Codecov](https://codecov.io/gh/boostorg/format/pull/56?src=pr&el=h1) Report  
> Merging [#56](https://codecov.io/gh/boostorg/format/pull/56?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/format/commit/87304f009b9d6533745cc0310e5ad96d8f67e696?src=pr&el=desc) will **increase** coverage by `0.74%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/format/pull/56/graphs/tree.svg?src=pr&token=9WwfaSgmDb&width=650&height=150)](https://codecov.io/gh/boostorg/format/pull/56?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #56      +/-   ##  
===========================================  
+ Coverage     67.6%   68.35%   +0.74%       
===========================================  
  Files           10       10                
  Lines          886      910      +24       
  Branches       299      299                
===========================================  
+ Hits           599      622      +23       
- Misses         108      109       +1       
  Partials       179      179  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/format/pull/56?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/format/exceptions.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZXhjZXB0aW9ucy5ocHA=) | | |  
| [include/boost/format/internals.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvaW50ZXJuYWxzLmhwcA==) | | |  
| [include/boost/format/alt\_sstream\_impl.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvYWx0X3NzdHJlYW1faW1wbC5ocHA=) | | |  
| [include/boost/format/alt\_sstream.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvYWx0X3NzdHJlYW0uaHBw) | | |  
| [include/boost/format/format\_class.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZm9ybWF0X2NsYXNzLmhwcA==) | | |  
| [include/boost/format/feed\_args.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZmVlZF9hcmdzLmhwcA==) | | |  
| [include/boost/format/format\_implementation.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZm9ybWF0X2ltcGxlbWVudGF0aW9uLmhwcA==) | | |  
| [include/boost/format/free\_funcs.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZnJlZV9mdW5jcy5ocHA=) | | |  
| [include/boost/format/parsing.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvcGFyc2luZy5ocHA=) | | |  
| [include/boost/format/group.hpp](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZ3JvdXAuaHBw) | | |  
| ... and [10 more](https://codecov.io/gh/boostorg/format/pull/56/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/format/pull/56?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/format/pull/56?src=pr&el=footer). Last update [87304f0...4191406](https://codecov.io/gh/boostorg/format/pull/56?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
