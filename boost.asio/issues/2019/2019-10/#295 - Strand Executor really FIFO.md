# #295 - Strand Executor really FIFO? [Closed]

> Username: benstadin  
> Created at: 2019-10-25 22:42:45 UTC  
> Updated at: 2020-12-30 01:10:43 UTC  
> Closed at: 2020-12-30 01:10:42 UTC  
> Url: https://github.com/boostorg/asio/issues/295  

According to the documentation, I thought that the Strand Executor is handling all calls in FIFO order. But is that really the case?   
  
Since strand_executor_service::enqueue() is using a mutex to lock, subsequent calls may be in random order when they try to acquire the mutex, since a mutex by design does not provider any kind of ordering.  
  
I made a quick check and added an atomic sequence id to scheduler_operation and recorded the order of the op sequence ids in ~on_invoker_exit() (where the next op is pushed onto the queue). They seem to be out of order there, though I might be missing something.   
  
Does it make sense to use a linearizable non-locking thread safe queue ([1], [2]) to avoid locking entirely on enque and deque?  
  
[1] https://github.com/max0x7ba/atomic_queue (queue is said to be lineariezable, according to https://github.com/max0x7ba/atomic_queue/issues/3)  
[2] https://github.com/facebook/folly/blob/master/folly/MPMCQueue.h

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-01 15:07:48 UTC  
> Updated at: 2019-11-01 15:09:30 UTC  
> Url: https://github.com/boostorg/asio/issues/295#issuecomment-548823303  

`net::strand` is working as designed. From https://cplusplus.github.io/networking-ts/draft.pdf  
  
3 A strand provides guarantees of ordering and non-concurrency. Given:  
(3.1) — strand objects s1 and s2 such that s1 == s2  
(3.2) — a function object f1 added to the strand s1 using post or defer, or using dispatch when s1.running_-  
in_this_thread() is false  
(3.3) — a function object f2 added to the strand s2 using post or defer, or using dispatch when s2.running_-  
in_this_thread() is false  
4 then the implementation invokes f1 and f2 such that:  
(4.1) — the invocation of f1 is not concurrent with the invocation of f2  
(4.2) — the invocation of f1 synchronizes with the invocation of f2.  
5 Furthermore, if the addition of f1 happens before the addition of f2, then the invocation of f1 happens  
before the invocation of f2.  
  
> Since strand_executor_service::enqueue() is using a mutex to lock, subsequent calls may be in random order when they try to acquire the mutex, since a mutex by design does not provider any kind of ordering.  
  
Function objects submitted by two calls to `enqueue` from the same thread, will always execute in the order they were submitted. However, function objects submitted from other threads may execute in between.

---

## Comment 2

> Username: benstadin  
> Created at: 2019-11-01 19:58:21 UTC  
> Url: https://github.com/boostorg/asio/issues/295#issuecomment-548929967  

> However, function objects submitted from other threads may execute in between.  
  
Thank you for the link. I‘d however interpret it that execution order is guaranteed also for other threads, also due to 5.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-01 20:06:15 UTC  
> Url: https://github.com/boostorg/asio/issues/295#issuecomment-548932420  

> Does it make sense to use a linearizable non-locking thread safe queue ([1], [2]) to avoid locking entirely on enque and deque?  
  
You are of course free to implement your own Executor similar to strand, which provides additional guarantees and experiment with it. Custom executors are fairly common (but unfortunately proprietary).

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:10:41 UTC  
> Url: https://github.com/boostorg/asio/issues/295#issuecomment-752293126  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#727](https://github.com/chriskohlhoff/asio/issues/727).
