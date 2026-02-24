# #297 - HELLO failures are not correctly logged [Closed]

> Username: anarthal  
> Created at: 2025-09-01 15:33:11 UTC  
> Updated at: 2025-09-04 14:48:01 UTC  
> Closed at: 2025-09-04 14:48:01 UTC  
> Url: https://github.com/boostorg/redis/issues/297  

Since logging happens [here](https://github.com/boostorg/redis/blob/da2f0101d01cf45b848a11a8936acf1d47cc5b4e/include/boost/redis/detail/resp3_handshaker.hpp#L48), if `has_error_in_response()` returns true, we log a success when we have a failure. This is what happens when you specify an incorrect password.

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-09-01 17:15:49 UTC  
> Url: https://github.com/boostorg/redis/issues/297#issuecomment-3242959537  

I have to investigate what exactly is happening. If the response contains an error the `generic_response` adapter will set it [here](https://github.com/boostorg/redis/blob/develop/include/boost/redis/adapter/detail/adapters.hpp#L197-L200). Therefore, I expect the logging to take [this](https://github.com/boostorg/redis/blob/develop/include/boost/redis/impl/connection_logger.ipp#L184-L189) branch.  
  
I think in general  if a RESP3 response contains an error, the adapters will set the `error_code` so `async_exec` also completes with an error. But I am not sure how this works in pipelines e.g. 10 commands are sent and all responses are read in the same `generic_response`.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-09-01 18:19:44 UTC  
> Url: https://github.com/boostorg/redis/issues/297#issuecomment-3243053468  

It's related to what we talked in #287 - it does not set the error code. I'm fixing it as part of #291
