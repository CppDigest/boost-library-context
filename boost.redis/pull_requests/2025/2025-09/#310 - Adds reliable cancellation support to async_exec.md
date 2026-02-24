# #310 Adds reliable cancellation support to async_exec [Merged]

> Username: anarthal  
> Created at: 2025-09-20 10:45:11 UTC  
> Updated at: 2025-09-26 10:50:46 UTC  
> Merged at: 2025-09-26 10:50:42 UTC  
> Closed at: 2025-09-26 10:50:42 UTC  
> Url: https://github.com/boostorg/redis/pull/310  

* Terminal cancellation in async_exec no longer tears down the connection when the cancelled request has been sent to the server.  
* Adds support for partial cancellation in async_exec with the same semantics.

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-09-20 10:50:26 UTC  
> Url: https://github.com/boostorg/redis/pull/310#issuecomment-3314892649  

This is a very early prototype, but the idea is changing how async_exec deals with cancellation so it's useful to the users. Right now, cancellation can either break the connection or do nothing, which makes it almost useless. With this done, we can freely use `asio::cancel_after` and go.  
  
The idea is that when a request gets cancelled but it has already been sent to the server, we keep an "abandoned request" item in the multiplexer to signal that we expect a response. When cancellation happens, the user operation finishes immediately, so the request and response objects they used can be freely destroyed. We keep this internal marker so we know that some RESP3 nodes will be incoming. Once they arrive, we discard them and continue with our lives.  
  
I want to merge #309 before this, since it makes reasoning about the multiplexer easier, and reduces the number of tests I need to write.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-24 21:19:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/310#pullrequestreview-3264771760  

📁 include/boost/redis/detail/multiplexer.hpp

```diff
  98 |+       // Marks the element as an abandoned request. An abandoned request
  99 |+       // won't cause problems when its response arrives, but that response will be ignored.
 100 |+       void mark_as_abandoned();
```

> Username: mzimbres  
> Created_at: 2025-09-24 21:19:10 UTC  
> Url: https://github.com/boostorg/redis/pull/310#discussion_r2377104715  

I think this should follow suit an be named `mark_abandoned`.


---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-24 21:22:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/310#pullrequestreview-3264779439  

📁 include/boost/redis/connection.hpp

```diff
 843 |+     *       arrives from the server, it will be ignored. In this situation, only
 844 |+     *       `asio::cancellation_type_t::terminal` and `asio::cancellation_type_t::partial`
 845 |+     *       are supported.
```

> Username: mzimbres  
> Created_at: 2025-09-24 21:22:11 UTC  
> Updated_at: 2025-09-24 21:22:32 UTC  
> Url: https://github.com/boostorg/redis/pull/310#discussion_r2377109893  

Perhaps complement  
  
> In this situation, only `asio::cancellation_type_t::terminal` and `asio::cancellation_type_t::partial`are supported, **`total` cancelation will be ignored**.


---

## Comment 4

> Username: mzimbres  
> Created_at: 2025-09-24 21:56:31 UTC  
> Url: https://github.com/boostorg/redis/pull/310#issuecomment-3330839006  

This is such a nice feature that I am wondering if we should add a section in the docs. It could be something along the lines of  
  
> async_exec operation can be cancelled, for example  
>   
>    conn.async_exec(req, resp, cancel_after(3s))  
>   
> will cause the request to be cancelled if not response was received after 3s have elapsed. If cancellation occurs before the request was sent, it will be remove from the connection internal queue with no side effects. Otherwise if the request was already sent, cancellation is still honored but the response to the cancelled request will be ignored by the connection. Notice that in the latter case the request might have had a side effect in the Redis server. For more details please refer to the async_exec docs.

---

## Comment 5

> Username: anarthal  
> Created_at: 2025-09-24 22:23:56 UTC  
> Url: https://github.com/boostorg/redis/pull/310#issuecomment-3330901610  

Completely agree with the docs section :) Except that cancel_after doesn't work yet ^^ we need to add some support for it in initiations, which I'll do in my next PR. I had thought of a broader section "Reliable connections and cancellation" where we could also explain cancel_if_unresponded and friends.

---
