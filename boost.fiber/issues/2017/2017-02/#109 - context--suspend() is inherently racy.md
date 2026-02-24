# #109 - context::suspend() is inherently racy [Closed]

> Username: avdgrinten  
> Created at: 2017-02-15 08:53:05 UTC  
> Updated at: 2017-02-17 06:56:57 UTC  
> Closed at: 2017-02-17 06:56:57 UTC  
> Url: https://github.com/boostorg/fiber/issues/109  

The purpose of the context::suspend() function is to enable low-overhead integration of other frameworks and boost::fiber. Using it however is almost impossible without introducing races. Assume that we want to integrate existing code that performs some asynchronous action. We want it to suspend the current fiber and resume it on completion. This results in the following code:  
  
    // Queue of all blocked fibers.  
    boost::intrusive::list<boost::fibers::context> queue;  
      
    // In a multithreaded environment we must protect the queue.  
    std::mutex mutex;  
      
    std::unique_lock<std::mutex> lock{mutex};  
    // Initiate the asynchronous action.  
    boost::fibers::active().wait_link(queue);  
    lock.unlock();  
    // RACE: Another thread can take the mutex, invoke set_ready() and yield() to the fiber  
    // here even though it is not suspended yet!  
    boost::fibers::context::suspend();  
  
I do not see how this race can be avoided with the current design of suspend(). Internally the race is avoided by using the suspend(detail::spin_lock &) overload. That interface however is not documented and inflexible.  
  
I propose that suspend() should be replaced either by  
  
    // Futex-style: Suspend the fiber. Then evaluate the predicate and immediately  
    // set_ready() the fiber if it evaluates to true.  
    template<typename P> // Must be a bool() function object.  
    void suspend_if(P predicate);  
  
or by  
  
    // Suspend the fiber and evaluate the cleanup function in the scheduler's context.  
    template<typename F>  
    void suspend(F cleanup);  
  
These functions would be more flexible than suspend(detail::spin_lock &): For example they allow synchronization via std::atomic instead of spinlocks. Another option would be implementing the proposed std::synchronic class for fibers.

---

## Comment 1

> Username: olk  
> Created at: 2017-02-15 09:03:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/109#issuecomment-279954415  

You should pass the lock to suspend(), the lock is released by the next fiber (worker-fiber or dispatcher-fiber) that will be resumed next. The lock is released when the fiber has been already suspended.  
  
```  
std::mutex mutex;  
std::unique_lock<std::mutex> lock{mutex};  
boost::fibers::active().wait_link(queue);  
boost::fibers::context::suspend(lock); // lock.unlock() is called by the fiber resumed next  
```

---

## Comment 2

> Username: avdgrinten  
> Created at: 2017-02-15 09:28:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/109#issuecomment-279960125  

That only works if BOOST_FIBERS_SPINLOCK_STD_MUTEX is set and it's undocumented. suspend(detail::spinlock &) should be documented if it is part of the API. It also is not compatible with more lightweight synchronization operations like std::atomic.

---

## Comment 3

> Username: olk  
> Created at: 2017-02-15 09:43:54 UTC  
> Url: https://github.com/boostorg/fiber/issues/109#issuecomment-279963681  

Why should it only work with BOOST_FIBERS_SPINLOCK_STD_MUTEX ?  
The spinlock implementations use std::atomics internally.

---

## Comment 4

> Username: avdgrinten  
> Created at: 2017-02-15 09:53:09 UTC  
> Updated at: 2017-02-15 09:53:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/109#issuecomment-279965741  

Well the suspend() method takes a detail::spinlock reference and that only equals std::mutex if BOOST_FIBERS_SPINLOCK_STD_MUTEX is set. That forces users to either set BOOST_FIBERS_SPINLOCK_STD_MUTEX and use std::mutex or use the implementation detail detail::spinlock. And at least it should be documented.  
  
It would be nice to have a mutex type agnostic suspend() method. What do you think about passing a functor to suspend() that runs in the next context and can release an arbitrary lock?

---

## Comment 5

> Username: olk  
> Created at: 2017-02-15 10:13:23 UTC  
> Updated at: 2017-02-15 10:49:36 UTC  
> Url: https://github.com/boostorg/fiber/issues/109#issuecomment-279970418  

In one of the previous versions I passed a functor to suspend() that has been called by the next fiber. I've removed it because it was of course slower than the current version.

---

## Comment 6

> Username: olk  
> Created at: 2017-02-15 11:57:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/109#issuecomment-279992497  

I do not intent those features as user's customization points.
