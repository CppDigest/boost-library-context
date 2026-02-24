# #52  Reimplement boost::fibers::asio::yield with promise/future. [Merged]

> Username: nat-goodspeed  
> Created at: 2015-08-22 18:42:50 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2015-08-23 08:08:54 UTC  
> Closed at: 2015-08-23 08:08:54 UTC  
> Url: https://github.com/boostorg/fiber/pull/52  

In essence, yield_t et al. become very like use_future_t et al. The only real  
difference is the async_result return type and get() method. Factor out common  
functionality into promise_completion_token and promise_handler.  
  
Remove the boost::fibers::asio::spawn() function and its basic_yield_context  
infrastructure. Fix up all existing references in example source code.

---

## Comment 1

> Username: olk  
> Created_at: 2015-08-23 08:09:03 UTC  
> Url: https://github.com/boostorg/fiber/pull/52#issuecomment-133798706  

very nice - thank you Nat!

---
