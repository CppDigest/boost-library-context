# #1838 Async compose 1773 [Closed]

> Username: madmongo1  
> Created at: 2020-02-05 14:40:03 UTC  
> Updated at: 2022-06-21 23:41:10 UTC  
> Closed at: 2022-06-21 23:41:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1838  

Track the conversion of Beast to use the async_compose mechanism in asio.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-05 14:52:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1838#pullrequestreview-353776624  

📁 include/boost/beast/core/detect_ssl.hpp

```diff
 564 | 
 616 |-         if(! cont)
 565 |+         if(! asio_handler_is_continuation(&self))
```

> Username: vinniefalco  
> Created_at: 2020-02-05 14:52:27 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375301898  

eww we don't want to use this anymore


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-05 14:52:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1838#pullrequestreview-353777025  

📁 include/boost/beast/core/detect_ssl.hpp

```diff
 355 |- // very best performance, for example when using Coroutines TS (`co_await`).
 356 |- 
 357 |- struct run_detect_ssl_op
```

> Username: vinniefalco  
> Created_at: 2020-02-05 14:52:57 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375302186  

We have to teach `async_initiate`


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-05 14:55:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1838#pullrequestreview-353779326  

📁 example/echo-op/echo_op.cpp

```diff
 165 |-     // allow the reenter and yield keywords to work.
 149 |+     // It is a requirement of async operations that the completion handler must be
 150 |+     // invoked as if by @ref post. The composed operation must know whether it is
```

> Username: vinniefalco  
> Created_at: 2020-02-05 14:55:41 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375304019  

`post` is not a Beast function so making it a `@ref` won't work. Use `net::post` instead of `@ref post`.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-05 14:56:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1838#pullrequestreview-353779986  

📁 example/echo-op/echo_op.cpp

```diff
 226 |+                                                   buffer_.capacity() - buffer_.size()),
 227 |+                             std::min<std::size_t>(65536,              // and over 65536 is too much.
 228 |+                                                   buffer_.max_size() - buffer_.size()));
```

> Username: vinniefalco  
> Created_at: 2020-02-05 14:56:28 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375304533  

\sigh

> Username: madmongo1  
> Created_at: 2020-02-05 14:59:05 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375306245  

An unfortunate oversight due to ide autoformatting whil copy/pasting the code. In order to compile it could no longer be a template class.

> Username: madmongo1  
> Created_at: 2020-02-05 14:59:08 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375306294  

An unfortunate oversight due to ide autoformatting whil copy/pasting the code. In order to compile it could no longer be a template class.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-05 14:57:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1838#pullrequestreview-353781265  

📁 example/echo-op/echo_op.cpp

```diff
 322 |+         "DynamicBuffer type requirements not met");
 323 |+ 
 324 |+     // Create the composed operation and launch it. @ref async_compose
```

> Username: vinniefalco  
> Created_at: 2020-02-05 14:57:56 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375305510  

This is not a Javadoc, and even if it was, `async_compose` is not a Beast function so using `@ref` on it won't work. You need to use `boost::asio::async_compose` here, not `net::async_compose` (because it isn't in the TS yet).


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-05 14:58:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1838#pullrequestreview-353781492  

📁 example/echo-op/echo_op.cpp

```diff
 326 |+     // handler and binding the correct executor and allocators.
 327 |+     // We must provide the implementation of the operation (echo_op) and
 328 |+     // pass the completion token plus any io_objects involved in the composed operation.
```

> Username: vinniefalco  
> Created_at: 2020-02-05 14:58:11 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375305676  

"I/O objects"


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-05 14:59:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1838#pullrequestreview-353782475  

📁 example/echo-op/echo_op.cpp

```diff
 338 |+             token,
 339 |+             stream
 340 |+         );
```

> Username: vinniefalco  
> Created_at: 2020-02-05 14:59:22 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375306437  

Example code can run wide, so this is also OK:  
```  
    return boost::asio::async_compose< CompletionToken, void(beast::error_code) >(  
            echo_op< AsyncStream, DynamicBuffer >(stream, buffer),  
            token,  
            stream  
        );  
```


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-05 15:00:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1838#pullrequestreview-353782967  

📁 example/echo-op/echo_op.cpp

```diff
 169 |+                 beast::bind_front_handler(
 170 |+                     std::move(self),
 171 |+                     echo_op::completion_tag(),
```

> Username: vinniefalco  
> Created_at: 2020-02-05 14:59:59 UTC  
> Updated_at: 2020-02-06 12:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#discussion_r375306825  

Yuck, I don't know if I want to teach `completion_tag`


---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2020-02-05 17:32:46 UTC  
> Updated_at: 2020-02-06 12:13:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#issuecomment-582522330  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1838?src=pr&el=h1) Report  
> Merging [#1838](https://codecov.io/gh/boostorg/beast/pull/1838?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c058567ec1b6b767343706cdc58f45e69d491dda?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `95.45%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1838/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1838?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1838      +/-   ##  
===========================================  
- Coverage    95.17%   95.17%   -0.01%       
===========================================  
  Files          156      156                
  Lines        11942    11935       -7       
===========================================  
- Hits         11366    11359       -7       
  Misses         576      576  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1838?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1838/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9pbXBsL3JlYWQuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detect\_ssl.hpp](https://codecov.io/gh/boostorg/beast/pull/1838/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGVjdF9zc2wuaHBw) | `94.54% <83.33%> (-0.54%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1838/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0%> (-0.2%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1838/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.6% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1838?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1838?src=pr&el=footer). Last update [c058567...0afc235](https://codecov.io/gh/boostorg/beast/pull/1838?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 10

> Username: madmongo1  
> Created_at: 2020-02-05 18:12:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#issuecomment-582539455  

What are we going to do with this? It feels as if async_compose is not quite finished.

---

## Comment 11

> Username: stale[bot]  
> Created_at: 2020-03-07 12:52:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1838#issuecomment-596085171  

This issue has been open for a while with no activity, has it been resolved?

---
