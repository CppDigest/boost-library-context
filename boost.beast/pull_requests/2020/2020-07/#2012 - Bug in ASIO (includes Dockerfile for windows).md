# #2012 Bug in ASIO (includes Dockerfile for windows) [Closed]

> Username: sdarwin  
> Created at: 2020-07-10 13:57:51 UTC  
> Updated at: 2020-07-13 12:57:12 UTC  
> Closed at: 2020-07-13 12:57:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2012  

Dockerfile to run tests on Microsoft Windows platform.  
  
You can observe an error during the tests:  
  
```  
.\boost/asio/detail/win_iocp_overlapped_ptr.hpp(145): error C2039: 'context': is not a member of 'boost::asio::io_context::basic_executor_type<std::allocator<void>,0>'  
...  
...failed compile-c-c++ bin.v2\libs\beast\example\advanced\server\msvc-14.2\release\asio.mode-nots\cxxstd-2a-iso\threading-multi\advanced_server.obj..  
```  
  
With 8 cpus, this occurs at around 200 targets completed.  
With 4 cpus, this occurs at around 6000 targets completed.  
With 2 cpus, reaches 9900 targets, and then a different error.  
  
It might be a race condition. To deal with this, the first part of tests.bat detects number-of-processors and then caps it. Can be adjusted.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2020-07-10 14:01:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2012#issuecomment-656692485  

Thank you. Will investigate post haste.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-07-10 14:30:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2012#issuecomment-656706326  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2012?src=pr&el=h1) Report  
> Merging [#2012](https://codecov.io/gh/boostorg/beast/pull/2012?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/11a3b932b27e1c50d1785c01cf1e02ccc345ea79&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2012/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2012?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2012      +/-   ##  
===========================================  
- Coverage    95.09%   95.08%   -0.01%       
===========================================  
  Files          153      153                
  Lines        11939    11939                
===========================================  
- Hits         11353    11352       -1       
- Misses         586      587       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2012?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2012/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0.00%> (-0.20%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2012?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2012?src=pr&el=footer). Last update [11a3b93...18fddf1](https://codecov.io/gh/boostorg/beast/pull/2012?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: madmongo1  
> Created_at: 2020-07-11 06:53:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2012#issuecomment-657003978  

It's not a race condition. There seems to be a bug in ASIO.  
  
I have informed Chris.  
  
This is extremely helpful. Thank you.

---

## Comment 4

> Username: madmongo1  
> Created_at: 2020-07-13 12:57:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2012#issuecomment-657544453  

Merged to develop.

---
