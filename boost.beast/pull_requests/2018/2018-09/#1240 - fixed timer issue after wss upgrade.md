# #1240 fixed timer issue after wss upgrade [Closed]

> Username: JarleStrand  
> Created at: 2018-09-02 11:40:09 UTC  
> Updated at: 2018-10-07 18:28:17 UTC  
> Closed at: 2018-10-07 18:28:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1240  

Fixes the timer bug mentioned in Issue 1091 for the advanced_server_flex example.  
  
The bug is related to a timer that is not cancelled after an upgrade to wss.   
  
Instead of cancelling the timer, this solution ensures that the timer expires immediately, and that the call-back detects that no actions should be performed.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-09-02 17:06:11 UTC  
> Updated_at: 2018-09-23 08:31:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1240#issuecomment-417944795  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1240?src=pr&el=h1) Report  
> Merging [#1240](https://codecov.io/gh/boostorg/beast/pull/1240?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1da229a27c6f0539d422bcedbcf47cfe2517164a?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1240/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1240?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1240      +/-   ##  
===========================================  
+ Coverage    95.52%   95.53%   +<.01%       
===========================================  
  Files          113      113                
  Lines        11064    11064                
===========================================  
+ Hits         10569    10570       +1       
+ Misses         495      494       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1240?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1240/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `92.42% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1240?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1240?src=pr&el=footer). Last update [1da229a...a32d09d](https://codecov.io/gh/boostorg/beast/pull/1240?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-09-23 02:42:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1240#issuecomment-423787561  

Well I had some plans for a more general fix but this pull request is looking better and better to me. One thing, can we please also fix the advanced-server example? It is here:  
https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp  
  
This is what I was thinking for the general solution:  
https://github.com/vinniefalco/beast/blob/timed_stream/include/boost/beast/experimental/core/timed_socket.hpp#L25  
  
It needs to be adapted for WebSocket so you can send a ping, probably I will make the "on timer" action customizable.

---

## Comment 3

> Username: JarleStrand  
> Created_at: 2018-09-23 08:36:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1240#issuecomment-423800725  

Hi, I updated the advanced-server example as well with the exact same changes. It seems to work ok.  
I agree with you that a more general fix would certainly be better, but at least this will fix some annoying bugs in major examples.

---
