# #376 - Better error messages when BOOST_ASIO_CONCURRENCY_HINT_UNSAFE is set [Open]

> Username: mzimbres  
> Created at: 2025-12-26 09:34:35 UTC  
> Updated at: 2025-12-26 09:34:35 UTC  
> Url: https://github.com/boostorg/redis/issues/376  

We should either support `BOOST_ASIO_CONCURRENCY_HINT_UNSAFE` or improve the error message when it is set (or any other [hint](https://www.boost.org/doc/libs/latest/doc/html/boost_asio/overview/core/configuration.html) that prevents `async_resolve`). At the moment constructing an `io_context` with `BOOST_ASIO_CONCURRENCY_HINT_UNSAFE` produces the following error messages  
  
```  
(Boost.Redis) Trying to connect to Redis server at 127.0.0.1:6379 (TLS disabled)  
(Boost.Redis) Connect: hostname resolution failed: Operation not supported [system:95]  
(Boost.Redis) Failed to connect to Redis server at 127.0.0.1:6379 (TLS disabled): Operation not supported [system:95]  
(Boost.Redis) Trying to connect to Redis server at 127.0.0.1:6379 (TLS disabled)  
(Boost.Redis) Connect: hostname resolution failed: Operation not supported [system:95]  
(Boost.Redis) Failed to connect to Redis server at 127.0.0.1:6379 (TLS disabled): Operation not supported [system:95]  
(Boost.Redis) Trying to connect to Redis server at 127.0.0.1:6379 (TLS disabled)  
(Boost.Redis) Connect: hostname resolution failed: Operation not supported [system:95]  
(Boost.Redis) Failed to connect to Redis server at 127.0.0.1:6379 (TLS disabled): Operation not supported [system:95]  
```
