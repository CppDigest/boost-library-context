# #148 null_in.hpp:28:17: warning: suggest braces around initialization of s… [Merged]

> Username: jonesmz  
> Created at: 2020-03-13 07:56:35 UTC  
> Updated at: 2020-05-20 17:29:47 UTC  
> Merged at: 2020-05-20 13:06:36 UTC  
> Closed at: 2020-05-20 13:06:36 UTC  
> Url: https://github.com/boostorg/process/pull/148  

…ubobject

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 08:26:52 UTC  
> Url: https://github.com/boostorg/process/pull/148#issuecomment-598606474  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/148?src=pr&el=h1) Report  
> Merging [#148](https://codecov.io/gh/boostorg/process/pull/148?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8?src=pr&el=desc) will **decrease** coverage by `4.04%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/148/graphs/tree.svg?width=650&token=AhunMqTSpA&height=150&src=pr)](https://codecov.io/gh/boostorg/process/pull/148?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #148      +/-   ##  
===========================================  
- Coverage    89.14%   85.09%   -4.05%       
===========================================  
  Files          110      110                
  Lines         3132     2563     -569       
===========================================  
- Hits          2792     2181     -611       
- Misses         340      382      +42  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/148?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/detail/posix/null\_in.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9udWxsX2luLmhwcA==) | `25% <ø> (ø)` | :arrow_up: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0% <0%> (-100%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/detail/posix/terminate.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC90ZXJtaW5hdGUuaHBw) | `0% <0%> (-80%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `18.18% <0%> (-75.94%)` | :arrow_down: |  
| [include/boost/process/detail/posix/basic\_cmd.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9iYXNpY19jbWQuaHBw) | `27.53% <0%> (-68.12%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0% <0%> (-61.54%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60% <0%> (-40%)` | :arrow_down: |  
| ... and [35 more](https://codecov.io/gh/boostorg/process/pull/148/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/148?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/148?src=pr&el=footer). Last update [668579e...80f8111](https://codecov.io/gh/boostorg/process/pull/148?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
