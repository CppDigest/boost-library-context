# #1927 Fix compilation errors with msvc /std:c++latest [Closed]

> Username: madmongo1  
> Created at: 2020-04-30 11:56:22 UTC  
> Updated at: 2020-06-19 16:10:18 UTC  
> Closed at: 2020-05-01 05:16:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1927  

fixes #1916  
fixes #1925

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-30 13:23:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1927#pullrequestreview-403513944  

📁 doc/qbk/08_design/3_websocket_zaphoyd.qbk

```diff
 177 |                 class DynamicBuffer,    // Supports user defined types
 178 |-                 class ReadHandler       // Handler is NOT type-erased
 178 |+                 BOOST_BEAST_ASYNC_TPARAM2 ReadHandler       // Handler is NOT type-erased
```

> Username: vinniefalco  
> Created_at: 2020-04-30 13:23:12 UTC  
> Updated_at: 2020-04-30 15:49:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1927#discussion_r418005364  

No we shouldn't change this because it is expository and not compiled


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-30 13:23:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1927#pullrequestreview-403514523  

📁 include/boost/beast/core/async_base.hpp

```diff
 462 |     // Asynchronously send a message multiple times, once per second
 463 |-     template <class AsyncWriteStream, class T, class WriteHandler>
 463 |+     template <class AsyncWriteStream, class T, BOOST_BEAST_ASYNC_TPARAM2 WriteHandler>
```

> Username: vinniefalco  
> Created_at: 2020-04-30 13:23:51 UTC  
> Updated_at: 2020-04-30 15:49:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1927#discussion_r418005822  

This is expository (i.e. in the docs) do we want the docs to show this macro?

> Username: madmongo1  
> Created_at: 2020-04-30 13:58:40 UTC  
> Updated_at: 2020-04-30 15:49:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1927#discussion_r418030963  

If we don't, how will users write async completion handlers?  
Should we perhaps spell out the different approaches for when concepts are enabled and not?

> Username: vinniefalco  
> Created_at: 2020-04-30 14:03:53 UTC  
> Updated_at: 2020-04-30 15:49:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1927#discussion_r418034794  

Users can use `BOOST_ASIO_COMPLETION_TOKEN_FOR`


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-30 13:26:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1927#pullrequestreview-403516778  

📁 test/doc/core_1_refresher.cpp

```diff
 327 | //[code_core_1_refresher_8
 328 |- template <class AsyncWriteStream, class WriteHandler>
 328 |+ template <class AsyncWriteStream, BOOST_BEAST_ASYNC_TPARAM2 WriteHandler>
```

> Username: vinniefalco  
> Created_at: 2020-04-30 13:26:25 UTC  
> Updated_at: 2020-04-30 15:49:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1927#discussion_r418007529  

This shows up in the documentation, I don't think we want to show this to users. It should explicilty use `BOOST_ASIO_COMPLETION_TOKEN_FOR` or it should just say `class`.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-30 13:27:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1927#pullrequestreview-403517988  

📁 test/doc/core_4_layers.cpp

```diff
 247 |         class MutableBufferSequence,
 248 |-         class ReadHandler =
 248 |+         BOOST_BEAST_ASYNC_TPARAM2 ReadHandler =
```

> Username: vinniefalco  
> Created_at: 2020-04-30 13:27:46 UTC  
> Updated_at: 2020-04-30 15:49:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1927#discussion_r418008489  

Same here, I dont think we want to show this in the docs.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-30 14:46:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1927#pullrequestreview-403593108  

📁 include/boost/beast/core/async_base.hpp

```diff
  79 |     // Asynchronously read into a buffer until the buffer is full, or an error occurs
  80 |-     template<class AsyncReadStream, class ReadHandler>
  80 |+     template<class AsyncReadStream, BOOST_BEAST_ASYNC_TPARAM2 ReadHandler>
```

> Username: vinniefalco  
> Created_at: 2020-04-30 14:46:36 UTC  
> Updated_at: 2020-04-30 15:49:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1927#discussion_r418066241  

This goes into the docs, I dont think we should be teaching `BOOST_BEAST_ASYNC_TPARAM2`


---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2020-05-01 00:21:05 UTC  
> Url: https://github.com/boostorg/beast/pull/1927#issuecomment-622187793  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1927?src=pr&el=h1) Report  
> Merging [#1927](https://codecov.io/gh/boostorg/beast/pull/1927?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/f5064e0c460664fa34fdad44e2379a2309d39685&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1927/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1927?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1927      +/-   ##  
===========================================  
- Coverage    95.13%   95.12%   -0.01%       
===========================================  
  Files          156      156                
  Lines        12032    12032                
===========================================  
- Hits         11447    11446       -1       
- Misses         585      586       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1927?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9pbXBsL3JlYWQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/impl/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYmFzaWNfc3RyZWFtLmhwcA==) | `86.09% <ø> (ø)` | |  
| [include/boost/beast/core/impl/flat\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9zdHJlYW0uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `99.01% <ø> (ø)` | |  
| [include/boost/beast/http/impl/write.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `92.43% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/accept.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/close.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5ocHA=) | `98.81% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `98.26% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <ø> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.68% <ø> (ø)` | |  
| ... and [5 more](https://codecov.io/gh/boostorg/beast/pull/1927/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1927?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1927?src=pr&el=footer). Last update [83fc001...fa87c31](https://codecov.io/gh/boostorg/beast/pull/1927?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
