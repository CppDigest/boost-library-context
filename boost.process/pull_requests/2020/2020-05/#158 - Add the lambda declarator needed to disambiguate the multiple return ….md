# #158 Add the lambda declarator needed to disambiguate the multiple return … [Merged]

> Username: acronce  
> Created at: 2020-05-14 15:28:32 UTC  
> Updated at: 2020-05-20 18:09:55 UTC  
> Merged at: 2020-05-20 12:39:24 UTC  
> Closed at: 2020-05-20 12:39:24 UTC  
> Url: https://github.com/boostorg/process/pull/158  

…values from these lambda functions as a bool. Before this change the return of a "false" was interpreted as an int, but the second return is a bool. This caused the following compile error when accessing environment variables under Xcode 11.4.1: Return type 'bool' must match previous return type 'int' when lambda expression has unspecified explicit return type

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-14 15:53:11 UTC  
> Url: https://github.com/boostorg/process/pull/158#issuecomment-628725559  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/158?src=pr&el=h1) Report  
> Merging [#158](https://codecov.io/gh/boostorg/process/pull/158?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `4.04%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/158/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/158?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #158      +/-   ##  
===========================================  
- Coverage    89.14%   85.09%   -4.05%       
===========================================  
  Files          110      110                
  Lines         3132     2563     -569       
===========================================  
- Hits          2792     2181     -611       
- Misses         340      382      +42       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/158?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/detail/posix/environment.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9lbnZpcm9ubWVudC5ocHA=) | `100.00% <ø> (+4.91%)` | :arrow_up: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/terminate.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC90ZXJtaW5hdGUuaHBw) | `0.00% <0.00%> (-80.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `18.18% <0.00%> (-75.94%)` | :arrow_down: |  
| [include/boost/process/detail/posix/basic\_cmd.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9iYXNpY19jbWQuaHBw) | `27.53% <0.00%> (-68.12%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| ... and [35 more](https://codecov.io/gh/boostorg/process/pull/158/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/158?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/158?src=pr&el=footer). Last update [668579e...3aba1f6](https://codecov.io/gh/boostorg/process/pull/158?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jonesmz  
> Created_at: 2020-05-20 17:35:35 UTC  
> Url: https://github.com/boostorg/process/pull/158#issuecomment-631620428  

This is a straight forward bug in XCode.  
  
There was never any ambiguity in the lambdas in question. Their compiler assuming that "false" is an int, and then erroring on the std::equals()... line is not something that should be worked around.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 17:56:02 UTC  
> Url: https://github.com/boostorg/process/pull/158#issuecomment-631631032  

It is something that needs a workaround if someone wants to use `XCode` & `boost.process` together.

---

## Comment 4

> Username: acronce  
> Created_at: 2020-05-20 18:09:55 UTC  
> Url: https://github.com/boostorg/process/pull/158#issuecomment-631637982  

Thanks for accepting the pull request.   
  
I agree that it's irritating to need this work around, but an explicit return type is not a bad thing.  
  
FYI, Xcode uses clang under the hood, so this really is a clang issue. Apple builds their own copy of clang with their own version number (not very helpful).   
  
The version returned by clang is "Apple clang version 11.0.3 (clang-1103.0.32.59)". According to wikipedia that maps to LLVM 9.0.0.

---
