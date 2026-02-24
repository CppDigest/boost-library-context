# #129 Minor tweaks to async_pipe.hpp [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 04:59:58 UTC  
> Updated at: 2021-02-18 03:22:27 UTC  
> Closed at: 2020-05-20 14:02:24 UTC  
> Url: https://github.com/boostorg/process/pull/129  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 06:03:33 UTC  
> Updated_at: 2020-03-13 06:17:50 UTC  
> Url: https://github.com/boostorg/process/pull/129#issuecomment-598565672  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/129?src=pr&el=h1) Report  
> Merging [#129](https://codecov.io/gh/boostorg/process/pull/129?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `6.65%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/129/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/129?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #129      +/-   ##  
===========================================  
- Coverage    89.14%   82.48%   -6.66%       
===========================================  
  Files          110      111       +1       
  Lines         3132     3009     -123       
===========================================  
- Hits          2792     2482     -310       
- Misses         340      527     +187       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/129?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/detail/posix/async\_pipe.hpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9hc3luY19waXBlLmhwcA==) | `77.77% <100.00%> (-17.47%)` | :arrow_down: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `15.38% <0.00%> (-78.74%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [test/run\_exe\_path.cpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-dGVzdC9ydW5fZXhlX3BhdGguY3Bw) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [test/search\_path.cpp](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree#diff-dGVzdC9zZWFyY2hfcGF0aC5jcHA=) | `63.63% <0.00%> (-36.37%)` | :arrow_down: |  
| ... and [80 more](https://codecov.io/gh/boostorg/process/pull/129/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/129?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/129?src=pr&el=footer). Last update [668579e...81d9504](https://codecov.io/gh/boostorg/process/pull/129?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 14:02:24 UTC  
> Url: https://github.com/boostorg/process/pull/129#issuecomment-631493271  

This will cause a bunch of linker errors since you removed all the `inline` keywords.

---

## Comment 3

> Username: jonesmz  
> Created_at: 2020-05-20 16:27:26 UTC  
> Url: https://github.com/boostorg/process/pull/129#issuecomment-631583508  

I removed the inline keyword on the in-class declaration, where the inline keyword is superfluous and redundant.  
  
The inline keyword is preserved on the definitions of the member functions, which is the only place the keyword has an effect.  
  
Please reconsider.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 16:57:13 UTC  
> Url: https://github.com/boostorg/process/pull/129#issuecomment-631599985  

You removed it from all the constructor definitions though. Did you test this?

---

## Comment 5

> Username: jonesmz  
> Created_at: 2020-05-20 17:07:48 UTC  
> Url: https://github.com/boostorg/process/pull/129#issuecomment-631605775  

I count 3 out-of-class definitions for async_pipe::async_pipe(), and I added the inline keyword to all of them.  
  
Is there an out-of-class definition that I missed?

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 17:43:36 UTC  
> Url: https://github.com/boostorg/process/pull/129#issuecomment-631624599  

Ah you're right, but why did you move it to the definitions?

---

## Comment 7

> Username: jonesmz  
> Created_at: 2020-05-20 17:52:19 UTC  
> Url: https://github.com/boostorg/process/pull/129#issuecomment-631629109  

The inline keyword is redundant when used on things inside of a class declaration.  
  
The fact that these functions are inline (or not inline) is not directly relevant to the consumer of the declaration, because it's not part of the API.  
  
The inline-ness is an implementation detail, so it should be only shown on the definition.

---

## Comment 8

> Username: jonesmz  
> Created_at: 2020-05-20 17:53:58 UTC  
> Url: https://github.com/boostorg/process/pull/129#issuecomment-631629962  

My motivation here was that I was very confused when trying to understand how async_pipe worked. The inline keyword made it more difficult than it needed to be to understand.

---
