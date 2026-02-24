# #459 Version 52 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-10 01:06:01 UTC  
> Updated at: 2017-06-11 02:50:13 UTC  
> Merged at: 2017-06-10 06:11:50 UTC  
> Closed at: 2017-06-10 06:11:50 UTC  
> Url: https://github.com/boostorg/beast/pull/459  

* flat_buffer is an AllocatorAwareContainer  
* Add drain_buffer class  
  
API Changes:  
  
* `auto_fragment` is a member of `stream`  
* `binary`, `text` are members of `stream`  
* read_buffer_size is a member of `stream`  
* read_message_max is a member of `stream`  
* `write_buffer_size` is a member of `stream`  
* `ping_callback` is a member of stream  
* Remove `opcode` from `read`, `async_read`  
* `read_frame` returns `bool` fin  
* `opcode` is private  
* finish(error_code&) is a BodyReader requirement  
  
# IMPORTANT: Actions Required:  
  
* Change call sites which use `auto_fragment` with `set_option` to call `stream::auto_fragment` instead.  
  
* Change call sites which use message_type with `set_option` to call `stream::binary` or `stream::text` instead.  
  
* Change call sites which use `read_buffer_size` with `set_option` to call `stream::read_buffer_size` instead.  
  
* Change call sites which use `read_message_max` with `set_option` to call `stream::read_message_max` instead.  
  
* Change call sites which use `write_buffer_size` with `set_option` to call `stream::write_buffer_size` instead.  
  
* Change call sites which use `ping_callback` with `set_option` to call `stream::ping_callback` instead.  
  
* Remove the `opcode` reference parameter from calls to synchronous and asynchronous read functions, replace the logic with calls to `stream::got_binary` and `stream::got_text` instead.  
  
* Remove the `frame_info` parameter from all read frame call sites  
  
* Check the return value 'fin' for calls to `read_frame`  
  
* Change ReadHandlers passed to `async_read_frame` to have the signature `void(error_code, bool fin)`, use the `bool` to indicate if the frame is the last frame.  
  
* Remove all occurrences of the `opcode` enum at call sites  
  
# Documentation Preview  
http://vinniefalco.github.io/stage/beast/v52/index.html

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-10 02:26:32 UTC  
> Updated_at: 2017-06-10 02:56:12 UTC  
> Url: https://github.com/boostorg/beast/pull/459#issuecomment-307536131  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=h1) Report  
> Merging [#459](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/13c64e343957518e91c722b87fb0cb343cf3ac31?src=pr&el=desc) will **decrease** coverage by `0.13%`.  
> The diff coverage is `91.91%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/459/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #459      +/-   ##  
==========================================  
- Coverage   93.36%   93.23%   -0.14%       
==========================================  
  Files          91       92       +1       
  Lines        6719     6736      +17       
==========================================  
+ Hits         6273     6280       +7       
- Misses        446      456      +10  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/detail/frame.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `93.54% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/option.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvb3B0aW9uLmhwcA==) | `100% <ø> (+15.78%)` | :arrow_up: |  
| [include/beast/websocket/impl/ping.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `93.25% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `88.23% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/rfc6455.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvcmZjNjQ1NS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/buffer\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `77.77% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/flat\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `66.66% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `80% <0%> (-20%)` | :arrow_down: |  
| ... and [10 more](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=footer). Last update [13c64e3...76402f7](https://codecov.io/gh/vinniefalco/Beast/pull/459?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
