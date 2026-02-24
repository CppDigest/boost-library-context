# #316 - Mixing `fiber::mutex` and normal threads [Open]

> Username: DanielMehlber  
> Created at: 2024-02-21 14:43:18 UTC  
> Updated at: 2024-05-30 15:46:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/316  

Hello dear developers,  
  
This is more a quesion than a bug: When using fibers, the mutex provided by this library `fiber::mutex` is recommended because a _normal_ `std::mutex` does not know about fibers and might cause deadlocks in certain scenarios. So far so good.  
  
I basically have a pool of worker threads executing fibers (like a task- or job-system where each job is a fiber) using the shared_work scheduler (just for context, not relevant). Due to shared-memory between jobs, these fibers often use mutexes and locks. So far, this is a clear scenario because the only ones trying to lock the `fiber::mutex` are fibers running on dedicated worker threads.  
  
However, the main thread (or any other _normal thread_) sometimes wants to access these shared resources as well. It also tries to aquire a lock on the `boost::fiber::mutex` **without being a fiber**. I first hoped that the `fiber::mutex` class would work both with fibers and normal threads alike, but it does not, right?   
  
Instead, I assume that these mutexes highjack the main thread's context, for instance, and stash it away regardless of it not being a fiber. This suspends and main threads execution and often results in a deadlock-ish scenario where the program is stuck.  
  
When looking at the mutex code, this behavior seems to be deliberate, not a bug.  
  
```cpp  
// in mutex.cpp  
void mutex::lock() {  
    while ( true ) {  
        context * active_ctx = context::active(); // without checking if this is even a fiber  
  
        detail::spinlock_lock lk{ wait_queue_splk_ };  
        if ( BOOST_UNLIKELY( active_ctx == owner_) ) {  
            throw lock_error{  
                    std::make_error_code( std::errc::resource_deadlock_would_occur),  
                    "boost fiber: a deadlock is detected" };  
        }  
        if ( nullptr == owner_) {  
            owner_ = active_ctx;  
            return;  
        }  
  
        wait_queue_.suspend_and_wait( lk, active_ctx); // also stashing away the context of a *normal* thread  
    }  
}  
```  
  
Am I right in the assumption that `fiber::mutex` cannot be mixed with normal threads? If you had a scenario in which both normal threads and fibers would try to access the same shared memory, how would you protect/synchronize it?  
  
Thanks in advance!

---

## Comment 1

> Username: bgemmill  
> Created at: 2024-05-30 15:46:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/316#issuecomment-2140011383  

From my experience with the library, it treats all stacks as fibers even the main one, so locking a fiber mutex from the main thread will just work.  
  
The thread's primary stack will just be blocked for the time it can't get the mutex. If there are no other fibers on that thread, that thread will appear idle.
