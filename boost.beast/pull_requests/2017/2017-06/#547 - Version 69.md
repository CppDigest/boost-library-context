# #547 Version 69 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-27 02:57:34 UTC  
> Updated at: 2017-06-27 22:38:08 UTC  
> Merged at: 2017-06-27 22:04:41 UTC  
> Closed at: 2017-06-27 22:04:41 UTC  
> Url: https://github.com/boostorg/beast/pull/547  

* basic_parser optimizations  
* Use BEAST_FALLTHROUGH to silence warnings  
* Serialize in one step when possible

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-27 04:10:46 UTC  
> Updated_at: 2017-06-27 22:38:08 UTC  
> Url: https://github.com/boostorg/beast/pull/547#issuecomment-311247872  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=h1) Report  
> Merging [#547](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/4269f354e8c7142a1015e1efe314d851fc5d3f86?src=pr&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/547/graphs/tree.svg?width=650&src=pr&token=Gq6MFA9JRF&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #547      +/-   ##  
==========================================  
- Coverage   93.74%   93.73%   -0.02%       
==========================================  
  Files          94       94                
  Lines        7150     7134      -16       
==========================================  
- Hits         6703     6687      -16       
  Misses        447      447  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/impl/ping.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `93.25% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/close.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `97.36% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `86.31% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.63% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/status.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc3RhdHVzLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/buffered\_read\_stream.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/verb.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvdmVyYi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `86.97% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `91.89% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `95.49% <100%> (+0.08%)` | :arrow_up: |  
| ... and [4 more](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=footer). Last update [4269f35...3e1061b](https://codecov.io/gh/vinniefalco/Beast/pull/547?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
