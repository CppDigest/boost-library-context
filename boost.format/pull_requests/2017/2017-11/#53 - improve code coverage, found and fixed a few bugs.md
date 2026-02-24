# #53 improve code coverage, found and fixed a few bugs [Merged]

> Username: jeking3  
> Created at: 2017-11-01 16:53:23 UTC  
> Updated at: 2017-11-01 22:22:33 UTC  
> Merged at: 2017-11-01 22:22:31 UTC  
> Closed at: 2017-11-01 22:22:31 UTC  
> Url: https://github.com/boostorg/format/pull/53  

Got parser up to 99.7% code coverage, a significant improvement.  Found three issues along the way:  
  
1. Using `%1$T` in "don't throw" mode would throw an unhandled exception.  
2. The parser allowed for a number to follow an asterisk, for example `%1$*4.*5d`.  
3. The examples were not actually executed to verify they are successful.  
  
Apologies for the whitespace cleanup applied by my editor (geany).

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-01 17:48:29 UTC  
> Updated_at: 2017-11-01 22:05:47 UTC  
> Url: https://github.com/boostorg/format/pull/53#issuecomment-341184787  

# [Codecov](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=h1) Report  
> Merging [#53](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/format/commit/1ed06cf3d92ee6f006e8ade2fce1dff6802f2f90?src=pr&el=desc) will **increase** coverage by `8.21%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/format/pull/53/graphs/tree.svg?height=150&width=650&token=9WwfaSgmDb&src=pr)](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #53      +/-   ##  
===========================================  
+ Coverage    79.59%   87.81%   +8.21%       
===========================================  
  Files           10       10                
  Lines          799      886      +87       
===========================================  
+ Hits           636      778     +142       
+ Misses         163      108      -55  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/format/parsing.hpp](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvcGFyc2luZy5ocHA=) | `97.3% <100%> (+14.52%)` | :arrow_up: |  
| [include/boost/format/format\_class.hpp](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZm9ybWF0X2NsYXNzLmhwcA==) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/format/group.hpp](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZ3JvdXAuaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/format/alt\_sstream.hpp](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvYWx0X3NzdHJlYW0uaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/format/alt\_sstream\_impl.hpp](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvYWx0X3NzdHJlYW1faW1wbC5ocHA=) | `42.64% <0%> (+0.71%)` | :arrow_up: |  
| [include/boost/format/feed\_args.hpp](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZmVlZF9hcmdzLmhwcA==) | `96.74% <0%> (+0.81%)` | :arrow_up: |  
| [include/boost/format/format\_implementation.hpp](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZm9ybWF0X2ltcGxlbWVudGF0aW9uLmhwcA==) | `92.41% <0%> (+1.67%)` | :arrow_up: |  
| [include/boost/format/internals.hpp](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvaW50ZXJuYWxzLmhwcA==) | `89.47% <0%> (+5.75%)` | :arrow_up: |  
| [include/boost/format/free\_funcs.hpp](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZnJlZV9mdW5jcy5ocHA=) | `94.11% <0%> (+17.64%)` | :arrow_up: |  
| ... and [1 more](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=footer). Last update [1ed06cf...50ba112](https://codecov.io/gh/boostorg/format/pull/53?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
