# #2677 - Boost Beast Webserver: Handling Shared Resources in an Async Enviornment [Closed]

> Username: WarrenN1  
> Created at: 2023-05-02 02:08:23 UTC  
> Updated at: 2023-10-28 16:31:30 UTC  
> Closed at: 2023-10-28 16:31:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2677  

The problem I am encountering is as follows:  
I have an io_loop with 5 worker threads. The 5 worker threads handle users. The problem is that some server resources have to be shared and because there are 5 worker threads those resources need to have protections (locks). The problem is that when locks have to be acquired for these, that operation is synchronous and blocking and I do not want to block my async server worker threads given there are only 5 worker threads. I am trying to design an asynchronous lock and this is my logic:  
  
1. I would like to use try_lock for the await_ready() but the problem is that it is undefined behavior if called from the same thread which is possible as in the io_loop Thread1 can call f1() which could acquire the lock via try_lock() but then suspend on some other task as Thread1 then calls f2() which uses try_lock() which is undefined behavior.  
2. My next thought is to use std::atomic and !atomic.exchange() in the await_ready(). The problem is then that then we need a queue of waiting coroutines, which is fine, but due to the multithreaded environment we would need a lock on that internal structure.  
  
I was wondering if anyone had any thoughts on the design or ways to adapt the current design of mutexes to an asynchronous enviornment.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-05-02 04:42:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2677#issuecomment-1530868923  

put it all on one thread and use no locks

---

## Comment 2

> Username: WarrenN1  
> Created at: 2023-05-02 05:14:47 UTC  
> Updated at: 2023-05-02 05:19:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2677#issuecomment-1530892378  

@vinniefalco I would like to do that in a perfect world. But the problem is that some information has to be shared. Do you have another suggestion? I am trying to make it so I could co_await the mutex while another process uses it and then notify waiting coroutines when unlock is called.  
  
If one worker thread is modifying the object the others shouldn't be able to and vis versa. The ideal behavior would be that any coroutine that is waiting on the lock is added to the queue of coroutines waiting on the lock. When the lock is unlocked, it notifies 1 of the waiting coroutines. If the lock is not currently locked, atomically acquire the lock.

---

## Comment 3

> Username: WarrenN1  
> Created at: 2023-05-02 05:23:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2677#issuecomment-1530897957  

Basically if the coroutine asks for the lock and no one owns it great it can continue. Otherwise, it is suspended indefinitely and added to a queue of coroutines waiting on the lock. When the lock is released via unlock, it notifies and resumes the first coroutine in that queue. The issue is with synchronization of adding to the queue for the same async lock fundamentally unless you have a different approach.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-05-02 09:46:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2677#issuecomment-1531183692  

Oh! You want an "asynchronous mutex" or semaphore. @klemens-morgenstern ?

---

## Comment 5

> Username: WarrenN1  
> Created at: 2023-05-03 04:28:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2677#issuecomment-1532442619  

Yeah @vinniefalco @klemens-morgenstern. Basically a mutex I can call mutex.async_lock() on that then suspends execution. My thought is that upon a lock being ready, std::atomic<bool> with exchange can be used to signal accesses immediately. If that fails and suspend is called, the coroutine could be added to a queue of waiters on the lock. When a coroutine finishes with the mutex, they call unlock and then it notifies one of the waiting coroutines to resume. The problem is generating a queue for the mutex that can be synchronized safely across io_worker threads or have atomic inserts/pops.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-05-10 23:26:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2677#issuecomment-1542930413  

That's in the works, I'd encourage you to try it out:   
https://klemens.dev/sam/  
https://github.com/klemens-morgenstern/sam/

---

## Comment 7

> Username: ashtum  
> Created at: 2023-08-19 05:49:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2677#issuecomment-1684838957  

@WarrenN1 Is this still open?
