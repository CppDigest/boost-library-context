# #134 Minor tweaks to error.hpp [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 05:51:10 UTC  
> Updated at: 2020-05-20 18:20:52 UTC  
> Closed at: 2020-05-20 14:05:02 UTC  
> Url: https://github.com/boostorg/process/pull/134  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 06:21:57 UTC  
> Updated_at: 2020-03-13 06:33:15 UTC  
> Url: https://github.com/boostorg/process/pull/134#issuecomment-598569961  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/134?src=pr&el=h1) Report  
> Merging [#134](https://codecov.io/gh/boostorg/process/pull/134?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `6.65%`.  
> The diff coverage is `0.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/134/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/134?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #134      +/-   ##  
===========================================  
- Coverage    89.14%   82.48%   -6.66%       
===========================================  
  Files          110      111       +1       
  Lines         3132     3009     -123       
===========================================  
- Hits          2792     2482     -310       
- Misses         340      527     +187       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/134?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `15.38% <0.00%> (-78.74%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [test/run\_exe\_path.cpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-dGVzdC9ydW5fZXhlX3BhdGguY3Bw) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [test/search\_path.cpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-dGVzdC9zZWFyY2hfcGF0aC5jcHA=) | `63.63% <0.00%> (-36.37%)` | :arrow_down: |  
| [test/shell\_path.cpp](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree#diff-dGVzdC9zaGVsbF9wYXRoLmNwcA==) | `66.66% <0.00%> (-33.34%)` | :arrow_down: |  
| ... and [79 more](https://codecov.io/gh/boostorg/process/pull/134/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/134?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/134?src=pr&el=footer). Last update [668579e...ae40ddd](https://codecov.io/gh/boostorg/process/pull/134?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jonesmz  
> Created_at: 2020-05-20 17:14:26 UTC  
> Url: https://github.com/boostorg/process/pull/134#issuecomment-631609250  

There are a lot of other improvements that this PR included beyond only the one function change.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 17:47:15 UTC  
> Url: https://github.com/boostorg/process/pull/134#issuecomment-631626429  

Which beside whitespace and replacing `typedef` with `using`?

---

## Comment 4

> Username: jonesmz  
> Created_at: 2020-05-20 18:00:17 UTC  
> Url: https://github.com/boostorg/process/pull/134#issuecomment-631633224  

Whitespace and `using` are valuable on their own merits.  
  
But to directly answer your question, minus those:     void on_error(Executor&, const std::error_code & ec) const noexcept(false)

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 18:05:01 UTC  
> Url: https://github.com/boostorg/process/pull/134#issuecomment-631635536  

Right, I saw that, what's the benefit? Isn"t that the exact same thing as having the on `noexcept` specifier?

---

## Comment 6

> Username: jonesmz  
> Created_at: 2020-05-20 18:09:37 UTC  
> Url: https://github.com/boostorg/process/pull/134#issuecomment-631637821  

noexcept(false) is explicitly saying "This function throws an exception".  
  
This is useful for static analysis tools, IDEs that do IntelliSense / code completion, and just in general makes it very clear to the end-user reading the function signature that the function is expected to throw.

---

## Comment 7

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 18:20:52 UTC  
> Url: https://github.com/boostorg/process/pull/134#issuecomment-631643267  

It"s in the detail namespace, no library consumer should ever see that part of the code.   
I am gonna keep it to the `noexcept` I added in my commit.

---
