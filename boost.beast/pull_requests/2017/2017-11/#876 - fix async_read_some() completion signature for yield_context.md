# #876 fix async_read_some() completion signature for yield_context [Closed]

> Username: cbodley  
> Created at: 2017-11-09 02:39:44 UTC  
> Updated at: 2017-11-09 09:10:18 UTC  
> Closed at: 2017-11-09 09:10:18 UTC  
> Url: https://github.com/boostorg/beast/pull/876  

this adds a test case that calls async_read_some() with a yield_context. the test fails to compile on the return statement of async_read_some():  
```  
 libs/beast/test/beast/http/read.cpp:328:50:   required from here  
./boost/beast/http/impl/read.ipp:544:28: error: void value not ignored as it ought to be  
     return init.result.get();  
```  
full error message here: https://pastebin.com/raw/Xfqx8kKR  
  
the async_completion in async_read_some() is just missing ReadHandler's size_t argument in its signature

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-09 03:27:12 UTC  
> Url: https://github.com/boostorg/beast/pull/876#issuecomment-343036678  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/876?src=pr&el=h1) Report  
> Merging [#876](https://codecov.io/gh/boostorg/beast/pull/876?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6eba0e8f9ef81bf38285a7eee260078684611e2b?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/876/graphs/tree.svg?token=Gq6MFA9JRF&width=650&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/876?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #876      +/-   ##  
===========================================  
- Coverage     95.3%   95.29%   -0.01%       
===========================================  
  Files          104      104                
  Lines        10456    10456                
===========================================  
- Hits          9965     9964       -1       
- Misses         491      492       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/876?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/876?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `95.63% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/876?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.02% <0%> (-0.49%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/876?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/876?src=pr&el=footer). Last update [6eba0e8...f30b222](https://codecov.io/gh/boostorg/beast/pull/876?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-11-09 03:55:11 UTC  
> Url: https://github.com/boostorg/beast/pull/876#issuecomment-343040297  

Weird...I thought I fixed this already. In any event thanks a ton for the submission!

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-11-09 09:10:18 UTC  
> Url: https://github.com/boostorg/beast/pull/876#issuecomment-343092130  

Merged to **version 140**

---
