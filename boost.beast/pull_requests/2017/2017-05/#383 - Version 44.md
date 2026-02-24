# #383 Version 44 [Merged]

> Username: vinniefalco  
> Created at: 2017-05-23 22:59:54 UTC  
> Updated at: 2017-05-24 22:04:35 UTC  
> Merged at: 2017-05-24 21:35:13 UTC  
> Closed at: 2017-05-24 21:35:13 UTC  
> Url: https://github.com/boostorg/beast/pull/383  

* Use BOOST_THROW_EXCEPTION  
* Tidy up read_size_helper and dynamic buffers  
* Require Boost 1.58.0 or later  
* Tidy up and make get_lowest_layer public  
* Use BOOST_STATIC_ASSERT  
* Fix async return values in docs  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v44/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-05-23 23:01:37 UTC  
> Updated_at: 2017-05-24 22:04:35 UTC  
> Url: https://github.com/boostorg/beast/pull/383#issuecomment-303557027  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=h1) Report  
> Merging [#383](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/dab679cb0a3cf51a5ecf4e52a832edcd88ff5609?src=pr&el=desc) will **increase** coverage by `0.1%`.  
> The diff coverage is `83.14%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/383/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff            @@  
##           master     #383     +/-   ##  
=========================================  
+ Coverage   93.36%   93.47%   +0.1%       
=========================================  
  Files          91       92      +1       
  Lines        6634     6542     -92       
=========================================  
- Hits         6194     6115     -79       
+ Misses        440      427     -13  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/detail/pmd\_extension.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BtZF9leHRlbnNpb24uaHBw) | `81.81% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `94.44% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/buffered\_read\_stream.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/async\_result.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2FzeW5jX3Jlc3VsdC5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/ostream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9vc3RyZWFtLmhwcA==) | `93.02% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/consuming\_buffers.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2NvbnN1bWluZ19idWZmZXJzLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/message\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2VfcGFyc2VyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `80.95% <ø> (+1.19%)` | :arrow_up: |  
| ... and [39 more](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=footer). Last update [dab679c...663c275](https://codecov.io/gh/vinniefalco/Beast/pull/383?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
