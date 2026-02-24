# #30 - Try replacing use_awaitable with deferred for boost .180 in the TCP server [Closed]

> Username: mzimbres  
> Created at: 2022-09-24 10:04:44 UTC  
> Updated at: 2022-10-08 20:23:44 UTC  
> Closed at: 2022-10-08 20:23:44 UTC  
> Url: https://github.com/boostorg/redis/issues/30  

Klemens has suggested replacing `use_awaitable` with `deferred` in boost 1.80 to improve performance in the [TCP echo server](examples/echo_server.cpp) and the [RESP3 echo server](benchmarks/cpp/asio/echo_server_direct.cpp). If there are any improvements the [graphs](benchmarks/benchmarks.md) should be also updated. Some background information  
  
> Any coroutine always allocates a frame, but that doesn't mean an async_op does. I.e. if you async_foo(use_awaitable), that'll return an awaitable that has a frame but, in boost 1.80 you can directly await async ops, e.g.: co_await async_foo(asio::deferred); then it doesn't need to allocate anything

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-10-03 18:13:10 UTC  
> Url: https://github.com/boostorg/redis/issues/30#issuecomment-1265842014  

Aedis CI does not support 1.80 yet, so this issue will have to wait more.

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-10-08 20:23:44 UTC  
> Url: https://github.com/boostorg/redis/issues/30#issuecomment-1272391746  

I couldn't observe any measurable improvement in performance.
