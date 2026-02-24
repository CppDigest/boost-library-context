# #150 pipe_in.hpp:30:17: warning: suggest braces around initialization of subobject [Merged]

> Username: jonesmz  
> Created at: 2020-03-13 07:58:12 UTC  
> Updated at: 2020-05-20 17:29:56 UTC  
> Merged at: 2020-05-20 13:05:29 UTC  
> Closed at: 2020-05-20 13:05:29 UTC  
> Url: https://github.com/boostorg/process/pull/150  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 08:44:47 UTC  
> Updated_at: 2020-03-13 08:52:21 UTC  
> Url: https://github.com/boostorg/process/pull/150#issuecomment-598612434  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/150?src=pr&el=h1) Report  
> Merging [#150](https://codecov.io/gh/boostorg/process/pull/150?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `6.65%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/150/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/150?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #150      +/-   ##  
===========================================  
- Coverage    89.14%   82.48%   -6.66%       
===========================================  
  Files          110      111       +1       
  Lines         3132     3009     -123       
===========================================  
- Hits          2792     2482     -310       
- Misses         340      527     +187       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/150?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/detail/posix/pipe\_in.hpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9waXBlX2luLmhwcA==) | `33.33% <ø> (-23.19%)` | :arrow_down: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `15.38% <0.00%> (-78.74%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [test/run\_exe\_path.cpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-dGVzdC9ydW5fZXhlX3BhdGguY3Bw) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [test/search\_path.cpp](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree#diff-dGVzdC9zZWFyY2hfcGF0aC5jcHA=) | `63.63% <0.00%> (-36.37%)` | :arrow_down: |  
| ... and [80 more](https://codecov.io/gh/boostorg/process/pull/150/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/150?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/150?src=pr&el=footer). Last update [668579e...8704416](https://codecov.io/gh/boostorg/process/pull/150?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
