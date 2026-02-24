# #131 Use move construction for start_dir [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 05:21:52 UTC  
> Updated at: 2021-02-18 03:24:33 UTC  
> Closed at: 2020-05-20 13:39:46 UTC  
> Url: https://github.com/boostorg/process/pull/131  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 06:47:45 UTC  
> Url: https://github.com/boostorg/process/pull/131#issuecomment-598576680  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/131?src=pr&el=h1) Report  
> Merging [#131](https://codecov.io/gh/boostorg/process/pull/131?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `4.04%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/131/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/131?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #131      +/-   ##  
===========================================  
- Coverage    89.14%   85.09%   -4.05%       
===========================================  
  Files          110      110                
  Lines         3132     2563     -569       
===========================================  
- Hits          2792     2181     -611       
- Misses         340      382      +42       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/131?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/start\_dir.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3N0YXJ0X2Rpci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/terminate.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC90ZXJtaW5hdGUuaHBw) | `0.00% <0.00%> (-80.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `18.18% <0.00%> (-75.94%)` | :arrow_down: |  
| [include/boost/process/detail/posix/basic\_cmd.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9iYXNpY19jbWQuaHBw) | `27.53% <0.00%> (-68.12%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| ... and [35 more](https://codecov.io/gh/boostorg/process/pull/131/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/131?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/131?src=pr&el=footer). Last update [668579e...bd0ae09](https://codecov.io/gh/boostorg/process/pull/131?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 13:39:46 UTC  
> Url: https://github.com/boostorg/process/pull/131#issuecomment-631479994  

Unneeded, `api::start_dir_init` copies anyhow.

---

## Comment 3

> Username: jonesmz  
> Created_at: 2020-05-20 17:11:25 UTC  
> Url: https://github.com/boostorg/process/pull/131#issuecomment-631607645  

The objective of the patches that add the use of std::move is to bring the API for boost::process to a state of api consistency, for most types. That would result in types that didn't use the pass-by-value-and-std-move idiom to be easily identifiable as unusual and worth additional attention.

---
