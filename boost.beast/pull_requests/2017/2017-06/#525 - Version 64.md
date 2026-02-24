# #525 Version 64 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-21 23:43:18 UTC  
> Updated at: 2017-06-23 02:12:53 UTC  
> Merged at: 2017-06-22 17:41:11 UTC  
> Closed at: 2017-06-22 17:41:11 UTC  
> Url: https://github.com/boostorg/beast/pull/525  

* Simplify buffered_read_stream composed op  
* Simplify ssl teardown composed op  
* Simplify websocket write_op  
* Exemplars are compiled code  
* Better User-Agent in examples  
* async_write requires a non-const message  
* Doc tidying  
* Add link_directories to cmake  
  
API Changes:  
  
* Remove make_serializer  
  
Actions Required:  
  
* Replace calls to make_serializer with variable declarations  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v64/

---

## Review 1 [Commented]

> Username: harrishancock  
> Created_at: 2017-06-21 23:55:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/525#pullrequestreview-45592663  

📁 doc/3_4_composed.qbk

```diff
 216 |+ * For operations which complete immediately (i.e. without calling an
 217 |+   intermediate initiating function), forgetting to use `io_service::post`
 218 |+   to invoke the final handler, breaking this guarantee of the initiating
```

> Username: harrishancock  
> Created_at: 2017-06-21 23:55:53 UTC  
> Updated_at: 2017-06-22 14:50:04 UTC  
> Url: https://github.com/boostorg/beast/pull/525#discussion_r123393800  

Maybe rephrase:  
  
> For operations which complete immediately (i.e. without calling an intermediate initiating function), forgetting to use `io_service::post` to invoke the final handler. **[full stop]** This breaks the following initiating function guarantee: ...

> Username: vinniefalco  
> Created_at: 2017-06-21 23:58:24 UTC  
> Updated_at: 2017-06-22 14:50:04 UTC  
> Url: https://github.com/boostorg/beast/pull/525#discussion_r123394067  

willfix


---

## Review 2 [Commented]

> Username: harrishancock  
> Created_at: 2017-06-22 00:01:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/525#pullrequestreview-45593341  

📁 example/echo-op/echo_op.cpp

```diff
 227 |+ //
 228 |+ // Our implementation just passes through the call to the hook
 229 |+ // associated with the final handler.
```

> Username: harrishancock  
> Created_at: 2017-06-22 00:01:30 UTC  
> Updated_at: 2017-06-22 14:50:04 UTC  
> Url: https://github.com/boostorg/beast/pull/525#discussion_r123394478  

Consider moving the comments from `asio_handler_is_continuation()` here.  
  
> Our implementation just passes ~through~ the call **through** to the hook associated with the final handler. Note that we structure the calls to permit argument dependent lookup to take effect (`using boost::asio::asio_handler_*;`).  
>  
> Also, always use std::addressof to pass the pointer to the handler, otherwise an unwanted overload of operator& may be called instead.

> Username: vinniefalco  
> Created_at: 2017-06-22 00:05:15 UTC  
> Updated_at: 2017-06-22 14:50:04 UTC  
> Url: https://github.com/boostorg/beast/pull/525#discussion_r123394902  

willfix


---

## Review 3 [Commented]

> Username: harrishancock  
> Created_at: 2017-06-22 00:05:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/525#pullrequestreview-45593845  

📁 example/echo-op/echo_op.cpp

```diff
  21 |+ 
  22 |+ //]
  23 |+     -> beast::async_return_type<CompletionToken, void(beast::error_code)>;
```

> Username: harrishancock  
> Created_at: 2017-06-22 00:05:38 UTC  
> Updated_at: 2017-06-22 14:50:04 UTC  
> Url: https://github.com/boostorg/beast/pull/525#discussion_r123394954  

I was originally going to say "missing semicolon," but now I see it. I presume you did the trailing return type because you're testing everything against a C++11 compiler?  
  
Now that I see the deduced return type version live, I'm no longer sure it's less confusing, since the prototypes presented no longer match. What do you think?

> Username: vinniefalco  
> Created_at: 2017-06-22 00:06:39 UTC  
> Updated_at: 2017-06-22 14:50:04 UTC  
> Url: https://github.com/boostorg/beast/pull/525#discussion_r123395038  

Right, this ensures that all snippets compile. I think `auto` looks better this early in the section. You realize that the audience for this material is going to just be a handful of people on the entire planet? LOL...  
  
...but if this just helps ONE person to write a great open source library on top of Beast it will have served its purpose.

> Username: harrishancock  
> Created_at: 2017-06-22 00:29:29 UTC  
> Updated_at: 2017-06-22 14:50:04 UTC  
> Url: https://github.com/boostorg/beast/pull/525#discussion_r123397412  

SGTM.

> Username: harrishancock  
> Created_at: 2017-06-22 00:33:10 UTC  
> Updated_at: 2017-06-22 14:50:04 UTC  
> Url: https://github.com/boostorg/beast/pull/525#discussion_r123397757  

Haha, yes. We are bikeshedding over quite the obscurity! And the Networking TS combined with the Coroutines TS may make this entire style of coding obsolete ...


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2017-06-22 00:12:39 UTC  
> Updated_at: 2017-06-22 18:59:19 UTC  
> Url: https://github.com/boostorg/beast/pull/525#issuecomment-310239339  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=h1) Report  
> Merging [#525](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/f68dc343e7c077caee8a95b2a59a2ccb9f979567?src=pr&el=desc) will **decrease** coverage by `0.02%`.  
> The diff coverage is `90.54%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/525/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #525      +/-   ##  
==========================================  
- Coverage    93.5%   93.48%   -0.03%       
==========================================  
  Files          95       95                
  Lines        7097     7075      -22       
==========================================  
- Hits         6636     6614      -22       
  Misses        461      461  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `90.47% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/drain\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RyYWluX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/async\_result.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2FzeW5jX3Jlc3VsdC5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `91.89% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/impl/buffered\_read\_stream.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `86.97% <86.53%> (-0.4%)` | :arrow_down: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `71.42% <0%> (+14.28%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=footer). Last update [f68dc34...e11a294](https://codecov.io/gh/vinniefalco/Beast/pull/525?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
