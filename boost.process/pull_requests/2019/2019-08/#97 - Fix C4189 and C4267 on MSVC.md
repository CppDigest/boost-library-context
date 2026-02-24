# #97 Fix C4189 and C4267 on MSVC [Closed]

> Username: tt4g  
> Created at: 2019-08-28 08:47:53 UTC  
> Updated at: 2019-11-22 05:11:30 UTC  
> Closed at: 2019-11-22 05:11:30 UTC  
> Url: https://github.com/boostorg/process/pull/97  

Fix the following warnings:  
  
* warning C4189: 'cnt': local variable is initialized but not referenced  
* warning C4267: 'argument' conversion from 'size_t' to 'boost::winapi::ULONG_', possible loss of data

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-08-28 09:14:26 UTC  
> Url: https://github.com/boostorg/process/pull/97#issuecomment-525657193  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/97?src=pr&el=h1) Report  
> Merging [#97](https://codecov.io/gh/boostorg/process/pull/97?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/6ccce9104ae91f6d07ac64c6d4e5fc0b57a649fe?src=pr&el=desc) will **increase** coverage by `8.29%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/97/graphs/tree.svg?width=650&token=AhunMqTSpA&height=150&src=pr)](https://codecov.io/gh/boostorg/process/pull/97?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #97      +/-   ##  
===========================================  
+ Coverage    80.73%   89.02%   +8.29%       
===========================================  
  Files          110      110                
  Lines         2621     3152     +531       
===========================================  
+ Hits          2116     2806     +690       
+ Misses         505      346     -159  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/97?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/detail/on\_exit.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9vbl9leGl0LmhwcA==) | `76.92% <0%> (-23.08%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/child\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9jaGlsZF9oYW5kbGUuaHBw) | `73.33% <0%> (-12.39%)` | :arrow_down: |  
| [include/boost/process/detail/posix/null\_out.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9udWxsX291dC5ocHA=) | `8.33% <0%> (-8.34%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `94.11% <0%> (-5.89%)` | :arrow_down: |  
| [include/boost/process/detail/posix/async\_out.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9hc3luY19vdXQuaHBw) | `57.69% <0%> (-2.92%)` | :arrow_down: |  
| [...clude/boost/process/detail/posix/wait\_for\_exit.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC93YWl0X2Zvcl9leGl0LmhwcA==) | `77.08% <0%> (-0.7%)` | :arrow_down: |  
| [include/boost/process/detail/handler.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9oYW5kbGVyLmhwcA==) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/process/detail/posix/close\_out.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9jbG9zZV9vdXQuaHBw) | `0% <0%> (ø)` | :arrow_up: |  
| [include/boost/process/handles.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2hhbmRsZXMuaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/process/io.hpp](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2lvLmhwcA==) | `100% <0%> (ø)` | :arrow_up: |  
| ... and [32 more](https://codecov.io/gh/boostorg/process/pull/97/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/97?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/97?src=pr&el=footer). Last update [6ccce91...6e5b77e](https://codecov.io/gh/boostorg/process/pull/97?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
