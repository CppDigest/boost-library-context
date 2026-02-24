# #1319 Enable explicit instantiation of websocket::stream [Closed]

> Username: djarek  
> Created at: 2018-11-23 23:43:41 UTC  
> Updated at: 2019-09-10 21:02:20 UTC  
> Closed at: 2018-11-27 16:35:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1319  

This enables users to improve compilation performance by explicitly instantiating the stream template in another TU.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-24 00:14:24 UTC  
> Updated_at: 2018-11-25 02:18:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1319#issuecomment-441332008  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1319?src=pr&el=h1) Report  
> Merging [#1319](https://codecov.io/gh/boostorg/beast/pull/1319?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/4a2fd11fa9c4442d3d102e3a82018197b25eed4c?src=pr&el=desc) will **decrease** coverage by `2.4%`.  
> The diff coverage is `91.05%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1319/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1319?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1319      +/-   ##  
===========================================  
- Coverage    94.48%   92.07%   -2.41%       
===========================================  
  Files          124      124                
  Lines        11469    11509      +40       
===========================================  
- Hits         10836    10597     -239       
- Misses         633      912     +279  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1319?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/accept.ipp](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaXBw) | `100% <100%> (+0.23%)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `73.17% <66.66%> (-26.83%)` | :arrow_down: |  
| [...clude/boost/beast/websocket/detail/stream\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3N0cmVhbV9iYXNlLmhwcA==) | `89% <90.9%> (+2.58%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `5% <0%> (-95%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `71.52% <0%> (-26.46%)` | :arrow_down: |  
| [include/boost/beast/zlib/inflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2luZmxhdGVfc3RyZWFtLmhwcA==) | `75% <0%> (-25%)` | :arrow_down: |  
| [include/boost/beast/zlib/deflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RlZmxhdGVfc3RyZWFtLmhwcA==) | `81.25% <0%> (-18.75%)` | :arrow_down: |  
| ... and [5 more](https://codecov.io/gh/boostorg/beast/pull/1319/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1319?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1319?src=pr&el=footer). Last update [4a2fd11...9ef7297](https://codecov.io/gh/boostorg/beast/pull/1319?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2018-11-24 00:24:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1319#pullrequestreview-178035136  

📁 include/boost/beast/websocket/detail/stream_base.hpp

```diff
 599 |+             BOOST_THROW_EXCEPTION(std::invalid_argument{
 600 |+                 "deflateSupported == false"});
 601 |+             }
```

> Username: vinniefalco  
> Created_at: 2018-11-24 00:24:26 UTC  
> Updated_at: 2018-11-24 21:04:41 UTC  
> Url: https://github.com/boostorg/beast/pull/1319#discussion_r236027796  

Something weird with the white space here

> Username: djarek  
> Created_at: 2018-11-24 21:04:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1319#discussion_r236053723  

Done.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2018-11-24 00:27:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1319#pullrequestreview-178035258  

📁 include/boost/beast/websocket/stream.hpp

```diff
 428 |-         return read_size_hint(initial_size,
 429 |-             is_deflate_supported{});
 425 |+         return this->read_size_hint_pmd(initial_size, rd_done_, rd_remain_, rd_fh_);
```

> Username: vinniefalco  
> Created_at: 2018-11-24 00:27:33 UTC  
> Updated_at: 2018-11-24 21:04:41 UTC  
> Url: https://github.com/boostorg/beast/pull/1319#discussion_r236027885  

needs a newline after the first parenthesis

> Username: djarek  
> Created_at: 2018-11-24 21:04:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1319#discussion_r236053721  

Done.


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2018-11-24 00:45:43 UTC  
> Url: https://github.com/boostorg/beast/pull/1319#issuecomment-441333510  

At first glance, looks pretty good

---
