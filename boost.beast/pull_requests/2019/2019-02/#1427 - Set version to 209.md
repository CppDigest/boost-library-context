# #1427 Set version to 209 [Merged]

> Username: vinniefalco  
> Created at: 2019-02-03 01:08:42 UTC  
> Updated at: 2019-02-10 14:15:31 UTC  
> Merged at: 2019-02-03 20:59:31 UTC  
> Closed at: 2019-02-03 20:59:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1427  

* Faster http::string_to_field  
* async_echo supports move-only handlers  
* test::stream maintains a handler work guard  
* Qualify buffer_copy, don't qualify buffer_size  
* Add dynamic_buffer_ref  
* Refactor quickref.xml

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-02-03 01:47:18 UTC  
> Updated_at: 2019-02-03 20:59:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1427#issuecomment-460015387  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1427?src=pr&el=h1) Report  
> Merging [#1427](https://codecov.io/gh/boostorg/beast/pull/1427?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/be29ce4c1e114c6c46ee99bfb4477e2342519df2?src=pr&el=desc) will **decrease** coverage by `0.15%`.  
> The diff coverage is `84.48%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1427/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1427?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1427      +/-   ##  
===========================================  
- Coverage    93.67%   93.51%   -0.16%       
===========================================  
  Files          128      130       +2       
  Lines        11344    11377      +33       
===========================================  
+ Hits         10626    10639      +13       
- Misses         718      738      +20  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1427?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/string\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `80.48% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/make\_printable.hpp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL21ha2VfcHJpbnRhYmxlLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/chunk\_encode.ipp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvY2h1bmtfZW5jb2RlLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/buffers\_suffix.hpp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfc3VmZml4LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/serializer.ipp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.47% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `86.41% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/buffer\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/dynamic\_buffer\_ref.hpp](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2R5bmFtaWNfYnVmZmVyX3JlZi5ocHA=) | `0% <0%> (ø)` | |  
| ... and [22 more](https://codecov.io/gh/boostorg/beast/pull/1427/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1427?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1427?src=pr&el=footer). Last update [be29ce4...18a5f43](https://codecov.io/gh/boostorg/beast/pull/1427?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
