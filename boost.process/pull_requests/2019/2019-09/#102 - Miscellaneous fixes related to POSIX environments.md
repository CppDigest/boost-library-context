# #102 Miscellaneous fixes related to POSIX environments [Merged]

> Username: res2k  
> Created at: 2019-09-11 09:35:54 UTC  
> Updated at: 2019-11-22 05:07:53 UTC  
> Merged at: 2019-11-22 05:07:53 UTC  
> Closed at: 2019-11-22 05:07:53 UTC  
> Url: https://github.com/boostorg/process/pull/102  

Notably, directly assigning a `wnative_environment` to a `native_environment` now works with POSIX environments. Previously, it failed to compile.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-09-11 09:58:42 UTC  
> Url: https://github.com/boostorg/process/pull/102#issuecomment-530311257  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/102?src=pr&el=h1) Report  
> Merging [#102](https://codecov.io/gh/boostorg/process/pull/102?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/6ccce9104ae91f6d07ac64c6d4e5fc0b57a649fe?src=pr&el=desc) will **increase** coverage by `8.26%`.  
> The diff coverage is `50%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/102/graphs/tree.svg?width=650&token=AhunMqTSpA&height=150&src=pr)](https://codecov.io/gh/boostorg/process/pull/102?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #102      +/-   ##  
===========================================  
+ Coverage    80.73%   88.99%   +8.26%       
===========================================  
  Files          110      110                
  Lines         2621     3154     +533       
===========================================  
+ Hits          2116     2807     +691       
+ Misses         505      347     -158  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/102?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/detail/posix/environment.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9lbnZpcm9ubWVudC5ocHA=) | `95.08% <50%> (+95.08%)` | :arrow_up: |  
| [include/boost/process/detail/on\_exit.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9vbl9leGl0LmhwcA==) | `76.92% <0%> (-23.08%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/child\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9jaGlsZF9oYW5kbGUuaHBw) | `73.33% <0%> (-12.39%)` | :arrow_down: |  
| [include/boost/process/detail/posix/null\_out.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9udWxsX291dC5ocHA=) | `8.33% <0%> (-8.34%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `94.11% <0%> (-5.89%)` | :arrow_down: |  
| [include/boost/process/detail/posix/async\_out.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9hc3luY19vdXQuaHBw) | `57.69% <0%> (-2.92%)` | :arrow_down: |  
| [...clude/boost/process/detail/posix/wait\_for\_exit.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC93YWl0X2Zvcl9leGl0LmhwcA==) | `77.08% <0%> (-0.7%)` | :arrow_down: |  
| [include/boost/process/detail/handler.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9oYW5kbGVyLmhwcA==) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/process/detail/posix/close\_out.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9jbG9zZV9vdXQuaHBw) | `0% <0%> (ø)` | :arrow_up: |  
| [include/boost/process/handles.hpp](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2hhbmRsZXMuaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| ... and [33 more](https://codecov.io/gh/boostorg/process/pull/102/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/102?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/102?src=pr&el=footer). Last update [6ccce91...40df789](https://codecov.io/gh/boostorg/process/pull/102?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
