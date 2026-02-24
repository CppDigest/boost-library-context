# #1168 Delay construction of BodyReader in parser [Closed]

> Username: djarek  
> Created at: 2018-06-19 19:39:00 UTC  
> Updated at: 2019-09-10 21:02:24 UTC  
> Closed at: 2019-03-01 19:07:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1168  

Enable usage of BodyReaders which need to inspect header contents in their constructor by delaying the construction of the reader until the header is complete.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-06-20 21:24:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1168#issuecomment-398902124  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1168?src=pr&el=h1) Report  
> Merging [#1168](https://codecov.io/gh/boostorg/beast/pull/1168?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/93c35524a6125db3e6aeefc8abc826a810dd5d61?src=pr&el=desc) will **decrease** coverage by `0.05%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1168/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/boostorg/beast/pull/1168?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1168      +/-   ##  
===========================================  
- Coverage    95.53%   95.48%   -0.06%       
===========================================  
  Files          112      112                
  Lines        11007    11024      +17       
===========================================  
+ Hits         10516    10526      +10       
- Misses         491      498       +7  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1168?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/parser.hpp](https://codecov.io/gh/boostorg/beast/pull/1168/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `83.92% <100%> (+0.9%)` | :arrow_up: |  
| [include/boost/beast/http/impl/parser.ipp](https://codecov.io/gh/boostorg/beast/pull/1168/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcGFyc2VyLmlwcA==) | `90% <100%> (-10%)` | :arrow_down: |  
| [include/boost/beast/http/empty\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/1168/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `50% <0%> (-22.73%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1168/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `92.32% <0%> (-0.11%)` | :arrow_down: |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/1168/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `92.55% <0%> (+0.17%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1168?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1168?src=pr&el=footer). Last update [93c3552...ca7fe18](https://codecov.io/gh/boostorg/beast/pull/1168?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-08-29 15:07:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1168#issuecomment-416988486  

Hmm.. I don't know how I feel about this.  Both the parser and serializer need to construct the associated reader and writer objects immediately so the caller can access them if necessary to perform implementation-specific tasks. For example to configure settings related to reading or writing (such as setting a buffer size or choosing an allocator).  
  
I realize that `parser` is deficient because it does not expose these interfaces yet (e.g. `parser::reader_impl()` to match `serializer::writer_impl()`) but they will need to be added. At that point we need to figure out how we want to get the headers to the reader. Perhaps it will be a separate function call.  
  
For now this has to wait until after Cppcon.

---

## Comment 3

> Username: stale[bot]  
> Created_at: 2018-10-28 15:48:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1168#issuecomment-433717059  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2018-10-28 18:50:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1168#issuecomment-433731230  

The reader/writer system needs more design work as I have uncovered more problems with it...this will have to wait until 2019 because of the wg21 meeting and the Boost release. Sorry for the delay!

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2019-03-01 19:08:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1168#issuecomment-468776443  

We will have to revisit this in the Great HTTP Refactor

---
