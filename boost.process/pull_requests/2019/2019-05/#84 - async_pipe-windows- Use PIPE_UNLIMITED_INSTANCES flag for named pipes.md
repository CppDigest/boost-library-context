# #84 async_pipe/windows: Use PIPE_UNLIMITED_INSTANCES flag for named pipes [Closed]

> Username: leha-bot  
> Created at: 2019-05-05 15:06:15 UTC  
> Updated at: 2019-05-08 09:22:42 UTC  
> Closed at: 2019-05-08 09:22:42 UTC  
> Url: https://github.com/boostorg/process/pull/84  

This PR fixes my reported bug #83

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-05-05 15:34:28 UTC  
> Url: https://github.com/boostorg/process/pull/84#issuecomment-489437180  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/84?src=pr&el=h1) Report  
> Merging [#84](https://codecov.io/gh/boostorg/process/pull/84?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/d13df2a194bba574d382ece7156fb3310d579e3b?src=pr&el=desc) will **decrease** coverage by `7.38%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/84/graphs/tree.svg?width=650&token=AhunMqTSpA&height=150&src=pr)](https://codecov.io/gh/boostorg/process/pull/84?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #84      +/-   ##  
===========================================  
- Coverage    87.01%   79.63%   -7.39%       
===========================================  
  Files          107       62      -45       
  Lines         3065     1257    -1808       
===========================================  
- Hits          2667     1001    -1666       
+ Misses         398      256     -142  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/84?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/detail/posix/handler.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9oYW5kbGVyLmhwcA==) | `0% <0%> (-25%)` | :arrow_down: |  
| [include/boost/process/detail/posix/signal.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaWduYWwuaHBw) | `66.66% <0%> (-20%)` | :arrow_down: |  
| [...clude/boost/process/detail/posix/wait\_for\_exit.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC93YWl0X2Zvcl9leGl0LmhwcA==) | `66.66% <0%> (-9.46%)` | :arrow_down: |  
| [include/boost/process/detail/posix/wait\_group.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC93YWl0X2dyb3VwLmhwcA==) | `66.03% <0%> (-8.25%)` | :arrow_down: |  
| [include/boost/process/detail/posix/terminate.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC90ZXJtaW5hdGUuaHBw) | `75% <0%> (-5%)` | :arrow_down: |  
| [...ude/boost/process/detail/posix/sigchld\_service.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaWdjaGxkX3NlcnZpY2UuaHBw) | `84.44% <0%> (-3.56%)` | :arrow_down: |  
| [include/boost/process/locale.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2xvY2FsZS5ocHA=) | `62% <0%> (-3.16%)` | :arrow_down: |  
| [include/boost/process/detail/posix/basic\_pipe.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9iYXNpY19waXBlLmhwcA==) | `86.66% <0%> (-3.08%)` | :arrow_down: |  
| [include/boost/process/pipe.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3BpcGUuaHBw) | `67.92% <0%> (-3.05%)` | :arrow_down: |  
| [include/boost/process/detail/posix/group\_ref.hpp](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9yZWYuaHBw) | `57.14% <0%> (-2.86%)` | :arrow_down: |  
| ... and [94 more](https://codecov.io/gh/boostorg/process/pull/84/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/84?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/84?src=pr&el=footer). Last update [d13df2a...63d21f3](https://codecov.io/gh/boostorg/process/pull/84?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
