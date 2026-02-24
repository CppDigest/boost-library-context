# #255 - async_run exits after a resolve, connect or TLS handshake failure [Closed]

> Username: anarthal  
> Created at: 2025-05-21 10:43:24 UTC  
> Updated at: 2025-06-29 17:02:23 UTC  
> Closed at: 2025-06-29 17:02:23 UTC  
> Url: https://github.com/boostorg/redis/issues/255  

After the first failure, `async_exec` returns, even if it's configured to retry indefinitely.

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-05-31 20:21:21 UTC  
> Url: https://github.com/boostorg/redis/issues/255#issuecomment-2925693156  

There are some important things to keep in mind when working on this issue. Boost.Redis implements only the simplest reconnection strategy where it waits for a fixed amount of time and then retries.  
  
The next natural step would be to support exponential backoff as [the nodejs client](https://redis.io/docs/latest/develop/clients/nodejs/connect/#reconnect-after-disconnection) does.  
  
More critical than exponential backoff is reconnecting over a sentinel, something two users already expressed interest. The complex thing about this however is that requests that set [cancel_on_connection_lost](https://github.com/boostorg/redis/blob/479068e7786284510362258d323da65654afeca1/include/boost/redis/request.hpp#L54) will remain suspended during failover and then will have to be executed in the new instance.  
  
I thought at first that I could offer built-in sentinel support, but I am not sure anymore because a proper use of sentinel might involve [subscribing](https://github.com/boostorg/redis/blob/479068e7786284510362258d323da65654afeca1/example/cpp20_resolve_with_sentinel.cpp#L31) to health events, that might bloat the current connection (see the _Pub/Sub messages_ section).  
  
A better strategy might be to improve my original idea of having a separate function to resolve address, see [here](https://github.com/boostorg/redis/blob/479068e7786284510362258d323da65654afeca1/example/cpp20_resolve_with_sentinel.cpp#L31).

---

## Comment 2

> Username: anarthal  
> Created at: 2025-06-01 12:15:33 UTC  
> Url: https://github.com/boostorg/redis/issues/255#issuecomment-2927129476  

I'd like to solve this without a functional change before the next Boost release cutoff.  
  
Still, I've read the Sentinel docs to have a look at what it would imply. I'll comment on #237 so the conversation sticks to the relevant issue, in case users want to add something.
