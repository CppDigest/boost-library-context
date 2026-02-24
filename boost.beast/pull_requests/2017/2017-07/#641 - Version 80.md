# #641 Version 80 [Merged]

> Username: vinniefalco  
> Created at: 2017-07-12 18:30:08 UTC  
> Updated at: 2017-07-15 04:23:17 UTC  
> Merged at: 2017-07-15 04:16:20 UTC  
> Closed at: 2017-07-15 04:16:20 UTC  
> Url: https://github.com/boostorg/beast/pull/641  

* Javadoc tidying  
* Add basic_dynamic_body.hpp  
* Shrink buffer_prefix_view  
* Remove unused file_path  
* Add basic_file_body.hpp  
* buffers_ref is Assignable  
  
HTTP  
  
* Shrink chunk header buffer sequence size  
  
API Changes:  
  
* Refactor chunked-encoding serialization  
  
Actions Required:  
  
* Remove references to ChunkDecorators. Use the new chunk-encoding  
  buffer sequences to manually produce a chunked payload body in  
  the case where control over the chunk-extensions and/or trailers  
  is required.  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v80/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-12 19:00:58 UTC  
> Url: https://github.com/boostorg/beast/pull/641#issuecomment-314865490  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=h1) Report  
> Merging [#641](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/3bcd9865f80f12ba5faad35c564918f85b02e271?src=pr&el=desc) will **decrease** coverage by `0.03%`.  
> The diff coverage is `94.73%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/641/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #641      +/-   ##  
===========================================  
- Coverage    93.86%   93.83%   -0.04%       
===========================================  
  Files          102      104       +2       
  Lines         7764     7831      +67       
===========================================  
+ Hits          7288     7348      +60       
- Misses         476      483       +7  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/flat\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/drain\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RyYWluX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/buffer\_prefix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9wcmVmaXguaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/consuming\_buffers.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2NvbnN1bWluZ19idWZmZXJzLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/buffer\_cat.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9jYXQuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/impl/buffer\_prefix.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyX3ByZWZpeC5pcHA=) | `95.45% <100%> (-0.5%)` | :arrow_down: |  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.42% <100%> (+0.01%)` | :arrow_up: |  
| [include/beast/core/detail/buffers\_ref.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9idWZmZXJzX3JlZi5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| ... and [18 more](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=footer). Last update [3bcd986...c8ecfe7](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-07-12 19:00:58 UTC  
> Updated_at: 2017-07-12 20:31:10 UTC  
> Url: https://github.com/boostorg/beast/pull/641#issuecomment-314865493  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=h1) Report  
> Merging [#641](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/3bcd9865f80f12ba5faad35c564918f85b02e271?src=pr&el=desc) will **decrease** coverage by `0.03%`.  
> The diff coverage is `94.73%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/641/graphs/tree.svg?token=Gq6MFA9JRF&width=650&height=150&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #641      +/-   ##  
===========================================  
- Coverage    93.86%   93.83%   -0.04%       
===========================================  
  Files          102      104       +2       
  Lines         7764     7831      +67       
===========================================  
+ Hits          7288     7348      +60       
- Misses         476      483       +7  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/flat\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/drain\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RyYWluX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/buffer\_cat.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9jYXQuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/consuming\_buffers.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2NvbnN1bWluZ19idWZmZXJzLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.42% <100%> (+0.01%)` | :arrow_up: |  
| [include/beast/core/buffer\_prefix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9wcmVmaXguaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/impl/consuming\_buffers.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvY29uc3VtaW5nX2J1ZmZlcnMuaXBw) | `95.08% <100%> (-0.08%)` | :arrow_down: |  
| [include/beast/core/impl/buffer\_prefix.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyX3ByZWZpeC5pcHA=) | `95.45% <100%> (-0.5%)` | :arrow_down: |  
| ... and [18 more](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=footer). Last update [3bcd986...c8ecfe7](https://codecov.io/gh/vinniefalco/Beast/pull/641?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
