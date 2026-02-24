# #640 refactor chunked serialization  [Closed]

> Username: vinniefalco  
> Created at: 2017-07-12 03:21:29 UTC  
> Updated at: 2017-07-15 04:23:31 UTC  
> Closed at: 2017-07-12 19:10:23 UTC  
> Url: https://github.com/boostorg/beast/pull/640  

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
http://vinniefalco.github.io/stage/beast/bjorn/beast/using_http/chunked_encoding.html

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-12 03:21:32 UTC  
> Url: https://github.com/boostorg/beast/pull/640#issuecomment-314633590  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=h1) Report  
> Merging [#640](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/3bcd9865f80f12ba5faad35c564918f85b02e271?src=pr&el=desc) will **decrease** coverage by `0.03%`.  
> The diff coverage is `98.14%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/640/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #640      +/-   ##  
===========================================  
- Coverage    93.86%   93.83%   -0.04%       
===========================================  
  Files          102      104       +2       
  Lines         7764     7839      +75       
===========================================  
+ Hits          7288     7356      +68       
- Misses         476      483       +7  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.42% <100%> (+0.01%)` | :arrow_up: |  
| [include/beast/core/detail/buffers\_ref.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9idWZmZXJzX3JlZi5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/chunk\_encode.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvY2h1bmtfZW5jb2RlLmlwcA==) | `100% <100%> (ø)` | |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `97.66% <100%> (-0.41%)` | :arrow_down: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `90.9% <92.3%> (-3.02%)` | :arrow_down: |  
| [include/beast/http/chunk\_encode.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2NodW5rX2VuY29kZS5ocHA=) | `95.23% <95.23%> (ø)` | |  
| [include/beast/http/detail/chunk\_encode.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9jaHVua19lbmNvZGUuaHBw) | `96.15% <95.65%> (+0.5%)` | :arrow_up: |  
| ... and [5 more](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=footer). Last update [3bcd986...3ffd817](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-07-12 03:21:32 UTC  
> Updated_at: 2017-07-12 14:01:17 UTC  
> Url: https://github.com/boostorg/beast/pull/640#issuecomment-314633591  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=h1) Report  
> Merging [#640](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/3bcd9865f80f12ba5faad35c564918f85b02e271?src=pr&el=desc) will **decrease** coverage by `0.02%`.  
> The diff coverage is `98.15%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/640/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #640      +/-   ##  
===========================================  
- Coverage    93.86%   93.84%   -0.03%       
===========================================  
  Files          102      104       +2       
  Lines         7764     7841      +77       
===========================================  
+ Hits          7288     7358      +70       
- Misses         476      483       +7  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/buffers\_ref.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9idWZmZXJzX3JlZi5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/chunk\_encode.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvY2h1bmtfZW5jb2RlLmlwcA==) | `100% <100%> (ø)` | |  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.42% <100%> (+0.01%)` | :arrow_up: |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `97.66% <100%> (-0.41%)` | :arrow_down: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `90.9% <92.3%> (-3.02%)` | :arrow_down: |  
| [include/beast/http/chunk\_encode.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2NodW5rX2VuY29kZS5ocHA=) | `95.23% <95.23%> (ø)` | |  
| [include/beast/http/detail/chunk\_encode.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9jaHVua19lbmNvZGUuaHBw) | `96.15% <95.65%> (+0.5%)` | :arrow_up: |  
| ... and [5 more](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=footer). Last update [3bcd986...8f1938f](https://codecov.io/gh/vinniefalco/Beast/pull/640?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 3 [Commented]

> Username: mpark  
> Created_at: 2017-07-12 03:33:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/640#pullrequestreview-49381552  

Just minor comments from a skim.

📁 doc/qbk/04_http/08_chunked_encoding.qbk

```diff
  15 |+ followed by a
  16 |+ [@https://tools.ietf.org/html/rfc7230#section-4.1 ['last chunk]].
  17 |+ Each chunked body may contain optional appliation-defined, connection-specific
```

> Username: mpark  
> Created_at: 2017-07-12 03:24:16 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126857085  

`s/appliation/application/`

> Username: vinniefalco  
> Created_at: 2017-07-12 03:38:09 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126858186  

willfix

---

📁 doc/qbk/04_http/08_chunked_encoding.qbk

```diff
  89 |+         [link beast.ref.beast__http__make_chunk `make_chunk`]
  90 |+ 
  91 |+         [link beast.ref.beast__http__make_chunk `make_chunk_last`]
```

> Username: mpark  
> Created_at: 2017-07-12 03:27:17 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126857327  

Is this supposed to point to `beast.ref.beast__http__make_chunk`?  
I do see [this page](http://vinniefalco.github.io/stage/beast/bjorn/beast/ref/beast__http__make_chunk_last.html) but not sure if this was a mistake.

> Username: vinniefalco  
> Created_at: 2017-07-12 03:35:54 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126858021  

Typo! willfix


---

## Review 4 [Changes requested]

> Username: glenfe  
> Created_at: 2017-07-12 03:38:27 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/640#pullrequestreview-49382724  

📁 include/beast/core/detail/buffers_ref.hpp

```diff
  33 |     buffers_ref(BufferSequence const& buffers)
  33 |-         : buffers_(buffers)
  34 |+         : buffers_(&buffers)
```

> Username: glenfe  
> Created_at: 2017-07-12 03:38:14 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126858191  

boost::addressof(buffers) instead of &buffers

> Username: vinniefalco  
> Created_at: 2017-07-12 03:40:20 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126858350  

Some day I will remember this on my own. Not today though.


---

## Review 5 [Changes requested]

> Username: glenfe  
> Created_at: 2017-07-12 03:45:05 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/640#pullrequestreview-49382996  

📁 include/beast/http/detail/chunk_encode.hpp

```diff
  41 |+     }
  42 |+ 
  43 |+     virtual
```

> Username: glenfe  
> Created_at: 2017-07-12 03:41:34 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126858453  

No need to repeat 'virtual' here; 'override' has you covered.

> Username: vinniefalco  
> Created_at: 2017-07-12 03:46:32 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126858879  

copypasta willfix

---

📁 include/beast/http/detail/chunk_encode.hpp

```diff
  56 |+ struct is_chunk_extensions<T, beast::detail::void_t<decltype(
  57 |+     std::declval<string_view&>() = std::declval<T&>().str(),
  58 |+         (void)0)>> : std::true_type
```

> Username: glenfe  
> Created_at: 2017-07-12 03:43:10 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126858581  

`void_t<expression,(void)0>` not necessary here, is it? Just:  `void_t<expression>` should suffice.

> Username: vinniefalco  
> Created_at: 2017-07-12 03:49:30 UTC  
> Updated_at: 2017-07-12 13:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/640#discussion_r126859142  

idk, I tend to just copy and paste these things without understanding them. I might copy this one and if I remove the `(void)0` you will hear me asking questions on Slack


---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-07-12 19:10:23 UTC  
> Url: https://github.com/boostorg/beast/pull/640#issuecomment-314867942  

Everything has been addressed in https://github.com/vinniefalco/Beast/pull/641 thanks!

---
