# #262 - Semaphore use in signals [Open]

> Username: yrashk  
> Created at: 2025-04-24 14:18:09 UTC  
> Updated at: 2025-08-27 20:54:56 UTC  
> Url: https://github.com/boostorg/interprocess/issues/262  

I am using interprocess' semaphore in conjunction with timed_wait, and I need to be able to handle SIGINT to terminate the waiting early. Looking into the implementation, I can see that I am getting the spin semaphore internally, which suggests to me that `post()`ing it from the signal handler is safe, since it is effectively just an atomic increment.   
  
This would likely not hold in other types of internal implementations of the semaphore. For example, while POSIX implementation seems to be fine in the first line (it just uses `sem_post`), it can later throw an exception.  
  
This leads me to two thoughts:  
  
1. Can we have a variant of the POSIX post() that would not throw an exception?  
2. Can we add compile-time introspection for signal safety of the chosen semaphore implementation? So that one can do something like `static_assert(boost::interprocess::interprocess_semaphore::signal_safe_post)`.  
  
I am happy to contribute such improvements if there's a consensus.

---

## Comment 1

> Username: yrashk  
> Created at: 2025-04-24 14:41:29 UTC  
> Updated at: 2025-04-24 15:18:42 UTC  
> Url: https://github.com/boostorg/interprocess/issues/262#issuecomment-2827897734  

Following up on (1):  
  
I can either do something like `std::error_code post(std::nothrow_t) noexcept` or `std::error_code try_post() noexcept`

---

## Comment 2

> Username: yrashk  
> Created at: 2025-08-27 20:54:55 UTC  
> Url: https://github.com/boostorg/interprocess/issues/262#issuecomment-3229714968  

Ping @igaztanaga – while I understand you have reservations about the chosen path in #263 that you rejected, I would still love to hear your opinion on the problem and potential solutions that align with your vision.  
  
I beleive this is still a very valid use case that would be great to handle.
