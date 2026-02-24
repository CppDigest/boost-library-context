# #938 Version 150 [Merged]

> Username: vinniefalco  
> Created at: 2017-12-15 01:59:42 UTC  
> Updated at: 2018-01-04 09:23:25 UTC  
> Merged at: 2017-12-16 03:44:06 UTC  
> Closed at: 2017-12-16 03:44:06 UTC  
> Url: https://github.com/boostorg/beast/pull/938  

* handler_ptr tests  
  
API Changes:  
  
* serializer::reader_impl is deprecated  
  
Actions Required:  
  
* Call serializer::writer_impl instead of reader_impl

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-12-15 02:13:14 UTC  
> Updated_at: 2017-12-15 18:06:32 UTC  
> Url: https://github.com/boostorg/beast/pull/938#issuecomment-351893637  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/938?src=pr&el=h1) Report  
> Merging [#938](https://codecov.io/gh/boostorg/beast/pull/938?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3edb30b2e8d64615fc8324c46d5485015bd4ab85?src=pr&el=desc) will **increase** coverage by `0.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/938/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/938?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #938      +/-   ##  
===========================================  
+ Coverage    95.67%   95.69%   +0.01%       
===========================================  
  Files          104      104                
  Lines        10424    10441      +17       
===========================================  
+ Hits          9973     9991      +18       
+ Misses         451      450       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/938?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/serializer.hpp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/buffer\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/string\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `80.48% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/basic\_file\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2ZpbGVfYm9keS5ocHA=) | `81.96% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/parser.ipp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcGFyc2VyLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/basic\_dynamic\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2R5bmFtaWNfYm9keS5ocHA=) | `85.29% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/parser.hpp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `83.01% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/span\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NwYW5fYm9keS5ocHA=) | `93.93% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/serializer.ipp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.71% <100%> (+0.15%)` | :arrow_up: |  
| [include/boost/beast/http/empty\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `72.72% <100%> (ø)` | :arrow_up: |  
| ... and [2 more](https://codecov.io/gh/boostorg/beast/pull/938/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/938?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/938?src=pr&el=footer). Last update [3edb30b...617fc42](https://codecov.io/gh/boostorg/beast/pull/938?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
