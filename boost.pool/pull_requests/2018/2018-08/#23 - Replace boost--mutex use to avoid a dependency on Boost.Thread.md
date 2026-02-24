# #23 Replace boost::mutex use to avoid a dependency on Boost.Thread [Merged]

> Username: pdimov  
> Created at: 2018-08-28 21:55:35 UTC  
> Updated at: 2018-08-29 03:57:37 UTC  
> Merged at: 2018-08-29 02:14:14 UTC  
> Closed at: 2018-08-29 02:14:14 UTC  
> Url: https://github.com/boostorg/pool/pull/23  

Pool is at level 15 in the dependency report at the moment, solely because of Boost.Thread.  
  
This also fixes the longstanding https://svn.boost.org/trac10/ticket/7085, I believe.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-08-28 22:25:59 UTC  
> Url: https://github.com/boostorg/pool/pull/23#issuecomment-416760927  

There is a test_threading.cpp which uses Boost.Thread to run threads in which Boost.Pool is being used.

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-08-28 22:50:39 UTC  
> Url: https://github.com/boostorg/pool/pull/23#issuecomment-416765869  

Correct, you still need Boost.Thread to run the tests, but most people don't do that.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-08-28 23:27:22 UTC  
> Updated_at: 2018-08-28 23:30:07 UTC  
> Url: https://github.com/boostorg/pool/pull/23#issuecomment-416772872  

# [Codecov](https://codecov.io/gh/boostorg/pool/pull/23?src=pr&el=h1) Report  
> Merging [#23](https://codecov.io/gh/boostorg/pool/pull/23?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/pool/commit/9daab9f821d4d047f50fea75a62b4f3130ad6a03?src=pr&el=desc) will **increase** coverage by `0.98%`.  
> The diff coverage is `54.54%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/pool/pull/23/graphs/tree.svg?width=650&token=blmk8CZxg2&height=150&src=pr)](https://codecov.io/gh/boostorg/pool/pull/23?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #23      +/-   ##  
===========================================  
+ Coverage    70.41%   71.39%   +0.98%       
===========================================  
  Files            8        8                
  Lines          436      444       +8       
  Branches       136      132       -4       
===========================================  
+ Hits           307      317      +10       
- Misses          28       30       +2       
+ Partials       101       97       -4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/pool/pull/23?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/pool/detail/mutex.hpp](https://codecov.io/gh/boostorg/pool/pull/23/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wb29sL2RldGFpbC9tdXRleC5ocHA=) | `54.54% <54.54%> (-45.46%)` | :arrow_down: |  
| [include/boost/pool/singleton\_pool.hpp](https://codecov.io/gh/boostorg/pool/pull/23/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wb29sL3NpbmdsZXRvbl9wb29sLmhwcA==) | `50% <0%> (+15.9%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/pool/pull/23?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/pool/pull/23?src=pr&el=footer). Last update [9daab9f...9def9a5](https://codecov.io/gh/boostorg/pool/pull/23?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
