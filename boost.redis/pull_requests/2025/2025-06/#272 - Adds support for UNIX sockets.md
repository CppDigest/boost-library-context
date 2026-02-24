# #272 Adds support for UNIX sockets [Merged]

> Username: anarthal  
> Created at: 2025-06-15 10:44:26 UTC  
> Updated at: 2025-06-20 11:23:43 UTC  
> Merged at: 2025-06-20 11:23:39 UTC  
> Closed at: 2025-06-20 11:23:39 UTC  
> Url: https://github.com/boostorg/redis/pull/272  

close #246

---

## Review 1 [Commented]

> Username: mzimbres  
> Created_at: 2025-06-15 13:11:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/272#pullrequestreview-2929395160  

📁 example/cpp20_unix_sockets.cpp

```diff
  31 |+    // If unix_socket is set to a non-empty string, UNIX sockets will be used
  32 |+    // instead of TCP. Set this value to the path where your server is listening.
  33 |+    // UNIX connections work in the same way as TCP connections.
```

> Username: mzimbres  
> Created_at: 2025-06-15 12:51:24 UTC  
> Updated_at: 2025-06-15 13:11:51 UTC  
> Url: https://github.com/boostorg/redis/pull/272#discussion_r2147859291  

_UNIX connections_ should be _UDS connections_

> Username: anarthal  
> Created_at: 2025-06-15 15:54:23 UTC  
> Url: https://github.com/boostorg/redis/pull/272#discussion_r2148358599  

Changed wording

---

📁 example/cpp20_unix_sockets.cpp

```diff
  52 |+ auto co_main(config) -> asio::awaitable<void>
  53 |+ {
  54 |+    std::cout << "Sorry, your system does not support UNIX sockets\n";
```

> Username: mzimbres  
> Created_at: 2025-06-15 12:53:42 UTC  
> Updated_at: 2025-06-15 13:11:51 UTC  
> Url: https://github.com/boostorg/redis/pull/272#discussion_r2147866288  

UNIX Domain Sockets instead of UNIX sockets


📁 include/boost/redis/detail/redis_stream.hpp

```diff
  57 |+    {
  58 |+       return cfg.unix_socket.empty() ? (cfg.use_ssl ? transport_type::tcp_tls : transport_type::tcp)
  59 |+                                      : transport_type::unix_socket;
```

> Username: mzimbres  
> Created_at: 2025-06-15 12:58:41 UTC  
> Updated_at: 2025-06-15 13:11:51 UTC  
> Url: https://github.com/boostorg/redis/pull/272#discussion_r2147880521  

I think this would be more readable as an if-else chain than ternary operators.

---

📁 include/boost/redis/detail/redis_stream.hpp

```diff
 179 |+    , stream_{ex, ssl_ctx_}
 180 |+ #ifdef BOOST_ASIO_HAS_LOCAL_SOCKETS
 181 |+    , unix_socket_{std::move(ex)}
```

> Username: mzimbres  
> Created_at: 2025-06-15 13:03:17 UTC  
> Updated_at: 2025-06-15 13:11:51 UTC  
> Url: https://github.com/boostorg/redis/pull/272#discussion_r2147893787  

I don't see the point of moving the executor here instead of copying since it is a lightweight object. The stream_ parameter above is copying it too.

> Username: anarthal  
> Created_at: 2025-06-15 14:39:32 UTC  
> Url: https://github.com/boostorg/redis/pull/272#discussion_r2148169163  

This is what the theory says, but unfortunately `asio::any_io_executor` does make a difference between copying and moving (think of it as a `std::function`): see how its base class [move](https://github.com/boostorg/asio/blob/62864c570afcb7234442168ecacd53af701d7d9e/include/boost/asio/execution/any_executor.hpp#L614-L631) and [copy](https://github.com/boostorg/asio/blob/62864c570afcb7234442168ecacd53af701d7d9e/include/boost/asio/execution/any_executor.hpp#L559-L573) differ. The performance difference is probably insignificant though.  
  
The stream copies it because it can only be moved once - after the move, the object becomes invalid (as a `std::function` would).

> Username: anarthal  
> Created_at: 2025-06-15 16:12:04 UTC  
> Url: https://github.com/boostorg/redis/pull/272#discussion_r2148404177  

I've updated this to make the move always happen, even if no UNIX sockets are supported.


---

## Comment 2

> Username: mzimbres  
> Created_at: 2025-06-15 13:14:34 UTC  
> Url: https://github.com/boostorg/redis/pull/272#issuecomment-2973828836  

Many thanks. I will try to finish the review today after kids go to bed :) I think we have to agree on naming, I am more used to the term Unix Domain socket (UDS) than UNIX sockets. I don't know which is better or more popular, I will look at how other libraries name it.

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-06-15 14:45:29 UTC  
> Url: https://github.com/boostorg/redis/pull/272#issuecomment-2973990722  

>Many thanks. I will try to finish the review today after kids go to bed :) I think we have to agree on naming, I am more used to the term Unix Domain socket (UDS) than UNIX sockets. I don't know which is better or more popular, I will look at how other libraries name it.  
  
I think the correct term is UNIX domain socket. However, I didn't immediately recognize the UDS acronym as 'UNIX domain socket', so I'd use the full term but avoid 'UDS'.  
  
As a reference:  
- The Redis server uses `unixsocket` and `unixsocketperm` as the config key names to set up UNIX domain sockets.  
- UNIX itself uses `AF_UNIX` for the address family.  
- redis-py uses `unix_socket_path` (https://github.com/redis/redis-py/issues/624)  
- The Rust library does not have a parameter per-se, but uses the "UNIX socket" term: https://docs.rs/redis/latest/redis/#connection-parameters  
  
I can rename `config::unix_socket` to `config::unix_domain_socket` or to `config::unix_socket_path`, and will update all comments to use "UNIX domain sockets"

---
