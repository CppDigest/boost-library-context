# #1032 *Works* WebSocket fixes for big endian processors [Closed]

> Username: smipi1  
> Created at: 2018-02-19 16:45:05 UTC  
> Updated at: 2018-02-21 22:50:06 UTC  
> Closed at: 2018-02-21 22:50:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1032  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2018-02-19 16:51:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1032#pullrequestreview-97606192  

📁 include/boost/beast/websocket/detail/frame.hpp

```diff
 275 |         buffer_copy(buffer(b), cb);
 275 |-         cr.code = big_uint16_to_native(&b[0]);
 276 |+         cr.code = (::new(&b[0]) big_uint16_buf_t)->value();
```

> Username: vinniefalco  
> Created_at: 2018-02-19 16:51:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1032#discussion_r169129277  

is this a separate bug?

> Username: smipi1  
> Created_at: 2018-02-19 16:53:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1032#discussion_r169129749  

Oops... Yes. Sorry.

> Username: vinniefalco  
> Created_at: 2018-02-19 16:56:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1032#discussion_r169130551  

if you could please submit that in a separate pull request I will add it to 156

> Username: vinniefalco  
> Created_at: 2018-02-20 19:08:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1032#discussion_r169426193  

This causes test failures and is generally not correct, it clobbers the value in `b`


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-02-19 21:13:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1032#issuecomment-366803951  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1032?src=pr&el=h1) Report  
> Merging [#1032](https://codecov.io/gh/boostorg/beast/pull/1032?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1a88cb147bf15541df632ca60f277a02068bae5b?src=pr&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1032/graphs/tree.svg?token=Gq6MFA9JRF&width=650&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1032?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1032      +/-   ##  
===========================================  
- Coverage    95.43%   95.42%   -0.02%       
===========================================  
  Files          106      106                
  Lines        10603    10573      -30       
===========================================  
- Hits         10119    10089      -30       
  Misses         484      484  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1032?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/detail/mask.hpp](https://codecov.io/gh/boostorg/beast/pull/1032/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL21hc2suaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/detail/frame.hpp](https://codecov.io/gh/boostorg/beast/pull/1032/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1032?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1032?src=pr&el=footer). Last update [1a88cb1...736583e](https://codecov.io/gh/boostorg/beast/pull/1032?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2018-02-21 22:50:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1032#issuecomment-367504439  

The is resolved in the latest version

---
