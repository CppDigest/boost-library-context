# #266 Adds a redis_stream class that encapsulates connection, reading and writing [Merged]

> Username: anarthal  
> Created at: 2025-06-06 17:36:54 UTC  
> Updated at: 2025-06-07 19:39:57 UTC  
> Merged at: 2025-06-07 17:15:42 UTC  
> Closed at: 2025-06-07 17:15:42 UTC  
> Url: https://github.com/boostorg/redis/pull/266  

Deprecates get_ssl_context for connection and basic_connection  
Deprecates reset_stream for connection and basic_connection

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-06-07 13:41:28 UTC  
> Url: https://github.com/boostorg/redis/pull/266#issuecomment-2952501700  

This PR is meant to facilitate the implementation of UNIX sockets. I've refactored the SSL stream, resolver and associated data into a single stream class that satisfies AsyncStream. The next PR will add a UNIX socket member to this class, effectively adding support for UNIX sockets.  
  
By doing this we can keep track of whether the SSL stream has been used before or not, effectively removing the need to call `reset_stream` entirely. #267 adds a test to check that this works fine (hopefully we can merge #267 before this one).  
I've deprecated reset_stream.  
  
I've also seen that `ssl::context` provides no const methods, so exposing a `const ssl::context& get_ssl_context() const` function is not very useful. I've deprecated it, too. Touching SSL contexts after they've been used to create streams is also technically disallowed by the OpenSSL docs.

---

## Review 2 [Approved]

> Username: mzimbres  
> Created_at: 2025-06-07 14:52:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/redis/pull/266#pullrequestreview-2907424079  

📁 include/boost/redis/detail/redis_stream.hpp

```diff
  87 |+ 
  88 |+             // Connect to the address that the resolver provided us
  89 |+             // TODO: is there a reason why this completion condition is here?
```

> Username: mzimbres  
> Created_at: 2025-06-07 14:47:50 UTC  
> Updated_at: 2025-06-07 14:52:44 UTC  
> Url: https://github.com/boostorg/redis/pull/266#discussion_r2133873565  

I believe I used this `async_connect` overload to address [this](https://github.com/chriskohlhoff/asio/issues/1476) problem. Perhaps it is already fixed.

---

📁 include/boost/redis/detail/redis_stream.hpp

```diff
 123 |+                // If this failed, we can't continue
 124 |+                if (ec) {
 125 |+                   self.complete(ec);
```

> Username: mzimbres  
> Created_at: 2025-06-07 14:50:56 UTC  
> Updated_at: 2025-06-07 14:52:44 UTC  
> Url: https://github.com/boostorg/redis/pull/266#discussion_r2133878082  

I think this should follow suit and complete with  
```cpp  
self.complete(ec == asio::error::operation_aborted ? error::ssl_handshake_timeout : ec);  
```


---
