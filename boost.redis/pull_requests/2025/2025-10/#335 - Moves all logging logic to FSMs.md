# #335 Moves all logging logic to FSMs [Merged]

> Username: anarthal  
> Created at: 2025-10-21 09:50:34 UTC  
> Updated at: 2025-10-22 10:00:03 UTC  
> Merged at: 2025-10-22 09:59:59 UTC  
> Closed at: 2025-10-22 09:59:59 UTC  
> Url: https://github.com/boostorg/redis/pull/335  

Replaces connection_logger by buffered_logger and log_utils.hpp  
Adds a system to log arbitrary values without defining new logger methods

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-21 09:56:19 UTC  
> Url: https://github.com/boostorg/redis/pull/335#issuecomment-3425716834  

This is an intent to make logging more self-contained. The idea is moving all logging logic away from the big `connection_logger` class into the FSMs, which have the context of what should be logged and why.  
  
I've renamed `connection_logger` to `buffered_logger`, which is just a logger + a `std::string` to reuse memory. All functions are now in a separate file `log_utils.hpp` which only gets included in `.ipp` files, with a hopefully positive effect on build times.  
  
The idea is that calling `log_debug(logger, args)` will do _the right thing_, formatting all arguments and performing the level check automatically. I've used some lightweight template metaprogramming to do this.   
  
After this, the idea is adjusting severities and messages accordingly to make logging more user friendly. Take [these lines](https://github.com/boostorg/redis/pull/335/files#diff-a6a9c4fecf5ae333a23ba46aa3b48d24b40270e13b47b71a9a17875b49c374bbR127-R131) for instance. I think it would make sense that the success and error branches had different log levels. But this will come later.  
  
Entails no functional change.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-10-22 09:59:50 UTC  
> Url: https://github.com/boostorg/redis/pull/335#issuecomment-3431487929  

Merging as per Marcelo's request.

---
