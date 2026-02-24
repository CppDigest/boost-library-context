# #133 Make constructor for codecvt_category_t constexpr [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 05:29:11 UTC  
> Updated at: 2020-05-20 17:58:42 UTC  
> Closed at: 2020-05-20 13:52:38 UTC  
> Url: https://github.com/boostorg/process/pull/133  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 05:55:59 UTC  
> Updated_at: 2020-03-13 06:09:16 UTC  
> Url: https://github.com/boostorg/process/pull/133#issuecomment-598563451  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/133?src=pr&el=h1) Report  
> Merging [#133](https://codecov.io/gh/boostorg/process/pull/133?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `6.65%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/133/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/133?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #133      +/-   ##  
===========================================  
- Coverage    89.14%   82.48%   -6.66%       
===========================================  
  Files          110      111       +1       
  Lines         3132     3009     -123       
===========================================  
- Hits          2792     2482     -310       
- Misses         340      527     +187       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/133?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/locale.hpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2xvY2FsZS5ocHA=) | `65.38% <ø> (+2.05%)` | :arrow_up: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `15.38% <0.00%> (-78.74%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [test/run\_exe\_path.cpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-dGVzdC9ydW5fZXhlX3BhdGguY3Bw) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [test/search\_path.cpp](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree#diff-dGVzdC9zZWFyY2hfcGF0aC5jcHA=) | `63.63% <0.00%> (-36.37%)` | :arrow_down: |  
| ... and [80 more](https://codecov.io/gh/boostorg/process/pull/133/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/133?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/133?src=pr&el=footer). Last update [668579e...bb9a666](https://codecov.io/gh/boostorg/process/pull/133?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 13:52:38 UTC  
> Url: https://github.com/boostorg/process/pull/133#issuecomment-631487503  

Doesn't work for C++11

---

## Comment 3

> Username: jonesmz  
> Created_at: 2020-05-20 16:28:13 UTC  
> Url: https://github.com/boostorg/process/pull/133#issuecomment-631583969  

Why are you supporting a 9 year old language?

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 16:58:55 UTC  
> Updated_at: 2020-05-20 16:59:52 UTC  
> Url: https://github.com/boostorg/process/pull/133#issuecomment-631600946  

LTS - at least I am trying. Not everyone can always update their compilers as quickly as they'd like to.

---

## Comment 5

> Username: jonesmz  
> Created_at: 2020-05-20 17:13:15 UTC  
> Url: https://github.com/boostorg/process/pull/133#issuecomment-631608592  

Ok. I suppose that's not a big problem for me.  
  
Can you leave this patch open, and tag it with some kind of "Requires C++14" tag?  
  
Unless you don't want the change for other reasons, of course.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 17:46:08 UTC  
> Url: https://github.com/boostorg/process/pull/133#issuecomment-631625896  

It is rather unlikely that I will build a C++14 version of this at any point. A C++20 boost.process2 is much more likely, i.e. a reference implementation of P1750R1.

---

## Comment 7

> Username: jonesmz  
> Created_at: 2020-05-20 17:58:42 UTC  
> Url: https://github.com/boostorg/process/pull/133#issuecomment-631632406  

Ok.

---
