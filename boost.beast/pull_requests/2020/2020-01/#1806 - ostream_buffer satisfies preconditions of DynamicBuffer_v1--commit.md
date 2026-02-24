# #1806 ostream_buffer satisfies preconditions of DynamicBuffer_v1::commit: [Closed]

> Username: madmongo1  
> Created at: 2020-01-17 17:52:03 UTC  
> Updated at: 2020-01-23 15:56:40 UTC  
> Closed at: 2020-01-23 15:56:40 UTC  
> Url: https://github.com/boostorg/beast/pull/1806  

```  
/home/rhodges/github/boostorg/boost/libs/beast/cmake-build-debug/test/beast/core/tests-beast-core  
beast.core.async_base  
beast.core.base64  
beast.core.basic_stream  
beast.core.bind_handler  
beast.core.buffer  
beast.core.buffer_traits  
beast.core.buffered_read_stream  
beast.core.buffers_adaptor  
beast.core.buffers_cat  
beast.core.buffers_prefix  
beast.core.buffers_range  
beast.core.buffers_suffix  
beast.core.buffers_to_string  
beast.core.clamp  
beast.core.detect_ssl  
beast.core.error  
beast.core.file_posix  
beast.core.file_stdio  
beast.core.flat_buffer  
beast.core.flat_static_buffer  
beast.core.flat_stream  
beast.core.get_io_context  
beast.core.make_printable  
beast.core.multi_buffer  
beast.core.ostream  
beast.core.rate_policy  
beast.core.read  
beast.core.read_size  
beast.core.saved_handler  
beast.core.sha1  
beast.core.span  
beast.core.static_buffer  
beast.core.static_string  
beast.core.stream_traits  
beast.core.string_param  
beast.core.tcp_stream  
beast.core.tuple  
beast.core.variant  
beast.core.varint  
291ms, 39 suites, 39 cases, 134956 tests total, 0 failures  
```

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-01-17 18:16:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1806#issuecomment-575737215  

commit message can read "ostream follows dynamic buffer preconditions"

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-01-18 00:39:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1806#issuecomment-575846377  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1806?src=pr&el=h1) Report  
> Merging [#1806](https://codecov.io/gh/boostorg/beast/pull/1806?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/46ac848fa46ce5b81f2bb5b221774d15c0cdd128?src=pr&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1806/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1806?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1806      +/-   ##  
===========================================  
- Coverage    95.17%   95.16%   -0.02%       
===========================================  
  Files          156      156                
  Lines        11949    11950       +1       
===========================================  
- Hits         11373    11372       -1       
- Misses         576      578       +2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1806?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/ostream.hpp](https://codecov.io/gh/boostorg/beast/pull/1806/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9vc3RyZWFtLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1806/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0%> (-0.9%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1806/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0%> (-0.2%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1806?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1806?src=pr&el=footer). Last update [46ac848...4a13c0c](https://codecov.io/gh/boostorg/beast/pull/1806?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-01-22 15:54:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1806#issuecomment-577253024  

The body of the commit message should read  
```  
close #1805  
```  
  
Because https://github.com/boostorg/beast/issues/1805

---
