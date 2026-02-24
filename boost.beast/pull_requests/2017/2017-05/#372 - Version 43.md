# #372 Version 43 [Merged]

> Username: vinniefalco  
> Created at: 2017-05-22 01:31:12 UTC  
> Updated at: 2017-05-22 20:19:30 UTC  
> Merged at: 2017-05-22 19:33:23 UTC  
> Closed at: 2017-05-22 19:33:23 UTC  
> Url: https://github.com/boostorg/beast/pull/372  

* Require Boost 1.64.0  
* Fix strict aliasing warnings in buffers_view  
* Tidy up buffer_prefix overloads and test  
* Add write limit to test::string_ostream  
* Additional constructors for consuming_buffers

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-05-22 02:09:35 UTC  
> Updated_at: 2017-05-22 20:19:30 UTC  
> Url: https://github.com/boostorg/beast/pull/372#issuecomment-302982365  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=h1) Report  
> Merging [#372](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/76f1084ecb010596d080ea96b9e47d15da649152?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/372/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #372      +/-   ##  
==========================================  
+ Coverage   93.36%   93.36%   +<.01%       
==========================================  
  Files          91       91                
  Lines        6584     6634      +50       
==========================================  
+ Hits         6147     6194      +47       
- Misses        437      440       +3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/buffer\_prefix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9wcmVmaXguaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/consuming\_buffers.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2NvbnN1bWluZ19idWZmZXJzLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/buffer\_cat.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyX2NhdC5pcHA=) | `96.39% <100%> (-0.86%)` | :arrow_down: |  
| [include/beast/core/impl/consuming\_buffers.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvY29uc3VtaW5nX2J1ZmZlcnMuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `79.76% <0%> (-1.2%)` | :arrow_down: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `96.23% <0%> (-0.27%)` | :arrow_down: |  
| [include/beast/http/impl/rfc7230.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcmZjNzIzMC5pcHA=) | `85.6% <0%> (ø)` | :arrow_up: |  
| [include/beast/http/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3JmYzcyMzAuaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `99.54% <0%> (ø)` | :arrow_up: |  
| ... and [3 more](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=footer). Last update [76f1084...dab679c](https://codecov.io/gh/vinniefalco/Beast/pull/372?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
