# #87 fix move assignment operators for basic_[io]pstream [Merged]

> Username: OBorce  
> Created at: 2019-06-04 22:29:01 UTC  
> Updated at: 2019-06-06 04:30:34 UTC  
> Merged at: 2019-06-06 04:30:34 UTC  
> Closed at: 2019-06-06 04:30:34 UTC  
> Url: https://github.com/boostorg/process/pull/87  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-06-04 22:53:29 UTC  
> Url: https://github.com/boostorg/process/pull/87#issuecomment-498872306  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/87?src=pr&el=h1) Report  
> Merging [#87](https://codecov.io/gh/boostorg/process/pull/87?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/0a554c92b589428d21e6e263350a1c0c866331ac?src=pr&el=desc) will **decrease** coverage by `6.18%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/87/graphs/tree.svg?width=650&token=AhunMqTSpA&height=150&src=pr)](https://codecov.io/gh/boostorg/process/pull/87?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #87      +/-   ##  
===========================================  
- Coverage    86.91%   80.73%   -6.19%       
===========================================  
  Files          111      110       -1       
  Lines         3256     2621     -635       
===========================================  
- Hits          2830     2116     -714       
- Misses         426      505      +79  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/87?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/pipe.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3BpcGUuaHBw) | `60% <ø> (-18.27%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/detail/posix/environment.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9lbnZpcm9ubWVudC5ocHA=) | `0% <0%> (-96.83%)` | :arrow_down: |  
| [include/boost/process/environment.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vudmlyb25tZW50LmhwcA==) | `0% <0%> (-94.96%)` | :arrow_down: |  
| [include/boost/process/detail/posix/terminate.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC90ZXJtaW5hdGUuaHBw) | `0% <0%> (-80%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0% <0%> (-61.54%)` | :arrow_down: |  
| [...ude/boost/process/detail/posix/sigchld\_service.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaWdjaGxkX3NlcnZpY2UuaHBw) | `36.73% <0%> (-51.27%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60% <0%> (-40%)` | :arrow_down: |  
| [include/boost/process/detail/posix/signal.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaWduYWwuaHBw) | `50% <0%> (-36.67%)` | :arrow_down: |  
| [include/boost/process/detail/posix/basic\_cmd.hpp](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9iYXNpY19jbWQuaHBw) | `66.66% <0%> (-30.48%)` | :arrow_down: |  
| ... and [39 more](https://codecov.io/gh/boostorg/process/pull/87/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/87?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/87?src=pr&el=footer). Last update [0a554c9...ca994c1](https://codecov.io/gh/boostorg/process/pull/87?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
