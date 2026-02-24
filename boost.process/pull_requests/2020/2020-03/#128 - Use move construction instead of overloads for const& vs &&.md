# #128 Use move construction instead of overloads for const& vs && [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 04:31:49 UTC  
> Updated at: 2021-02-18 03:22:07 UTC  
> Closed at: 2020-05-20 14:02:43 UTC  
> Url: https://github.com/boostorg/process/pull/128  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 05:38:42 UTC  
> Updated_at: 2020-03-13 05:44:31 UTC  
> Url: https://github.com/boostorg/process/pull/128#issuecomment-598558684  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/128?src=pr&el=h1) Report  
> Merging [#128](https://codecov.io/gh/boostorg/process/pull/128?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `6.65%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/128/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/128?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #128      +/-   ##  
===========================================  
- Coverage    89.14%   82.48%   -6.66%       
===========================================  
  Files          110      111       +1       
  Lines         3132     3009     -123       
===========================================  
- Hits          2792     2482     -310       
- Misses         340      527     +187       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/128?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/detail/basic\_cmd.hpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9iYXNpY19jbWQuaHBw) | `92.30% <100.00%> (-5.20%)` | :arrow_down: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `15.38% <0.00%> (-78.74%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [test/run\_exe\_path.cpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-dGVzdC9ydW5fZXhlX3BhdGguY3Bw) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [test/search\_path.cpp](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree#diff-dGVzdC9zZWFyY2hfcGF0aC5jcHA=) | `63.63% <0.00%> (-36.37%)` | :arrow_down: |  
| ... and [80 more](https://codecov.io/gh/boostorg/process/pull/128/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/128?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/128?src=pr&el=footer). Last update [668579e...bd2047f](https://codecov.io/gh/boostorg/process/pull/128?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jonesmz  
> Created_at: 2020-05-20 16:25:20 UTC  
> Url: https://github.com/boostorg/process/pull/128#issuecomment-631582273  

Why did you close?

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 16:55:42 UTC  
> Url: https://github.com/boostorg/process/pull/128#issuecomment-631599143  

Moving is more expensive than passing a reference. Not that it matters much, but technically your solution would be slower.

---

## Comment 4

> Username: jonesmz  
> Created_at: 2020-05-20 17:03:45 UTC  
> Url: https://github.com/boostorg/process/pull/128#issuecomment-631603659  

I strongly disagree.  
  
This change is in very small functions that are implicitly inline.  
  
I would be surprised if there was any difference in resulting assembly code, but this PR makes the code much easier to read and understand, and should be slightly faster to compile.

---
