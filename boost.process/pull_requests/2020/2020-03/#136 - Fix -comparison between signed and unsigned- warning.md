# #136 Fix "comparison between signed and unsigned" warning [Merged]

> Username: jonesmz  
> Created at: 2020-03-13 06:03:45 UTC  
> Updated at: 2020-05-20 17:18:51 UTC  
> Merged at: 2020-05-20 13:51:32 UTC  
> Closed at: 2020-05-20 13:51:32 UTC  
> Url: https://github.com/boostorg/process/pull/136  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 06:56:36 UTC  
> Url: https://github.com/boostorg/process/pull/136#issuecomment-598579119  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/136?src=pr&el=h1) Report  
> Merging [#136](https://codecov.io/gh/boostorg/process/pull/136?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `4.04%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/136/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/136?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #136      +/-   ##  
===========================================  
- Coverage    89.14%   85.09%   -4.05%       
===========================================  
  Files          110      110                
  Lines         3132     2563     -569       
===========================================  
- Hits          2792     2181     -611       
- Misses         340      382      +42       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/136?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [test/async\_system\_stackless.cpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-dGVzdC9hc3luY19zeXN0ZW1fc3RhY2tsZXNzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/terminate.hpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC90ZXJtaW5hdGUuaHBw) | `0.00% <0.00%> (-80.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `18.18% <0.00%> (-75.94%)` | :arrow_down: |  
| [include/boost/process/detail/posix/basic\_cmd.hpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9iYXNpY19jbWQuaHBw) | `27.53% <0.00%> (-68.12%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| ... and [36 more](https://codecov.io/gh/boostorg/process/pull/136/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/136?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/136?src=pr&el=footer). Last update [668579e...d3e4cbf](https://codecov.io/gh/boostorg/process/pull/136?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jonesmz  
> Created_at: 2020-03-13 08:00:52 UTC  
> Url: https://github.com/boostorg/process/pull/136#issuecomment-598597876  

Something is wrong with the unit tests on macosx.  
  
This change should have had no effect on, for example,  
  
bind_stdout.cpp:131: error: in "bind_stdout/nul": check 0 == exit_code has failed [0 != 11]  
  
  
But it's failing.

---
