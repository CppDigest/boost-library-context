# #195 - Strands hierarchy [Closed]

> Username: traceon  
> Created at: 2019-01-13 10:51:01 UTC  
> Updated at: 2020-12-30 00:59:34 UTC  
> Closed at: 2020-12-30 00:59:33 UTC  
> Url: https://github.com/boostorg/asio/issues/195  

Is there a way to build strands hierarchies, with the expected behavior being this: assuming `s_sub1` and `s_sub2` have `s_sup` as their parent, any task, submitted for `s_sub1` or `s_sub2` won't be executed until there are queued or running tasks for `s_sup`?  
  
Is it allowed at all, to wrap one strand with another using `boost::asio::strand`?  
  
Also, `boost::asio::strand` "provides serialised function invocation for any executor type", however there is `boost::asio::io_context::strand` which has its own implementation, and, surprisingly, is not defined simply as `boost::asio::strand<boost::asio::io_context::executor_type>`. Will the latter work the same way as the original `boost::asio::io_context::strand`?

---

## Comment 1

> Username: djarek  
> Created at: 2019-01-13 15:03:35 UTC  
> Updated at: 2019-01-13 15:04:23 UTC  
> Url: https://github.com/boostorg/asio/issues/195#issuecomment-453836950  

There is no way to build "hierarchies" of strands. Note that the problem you're trying to solve seems to be similar to task dependency management, which is out of scope of the executors in ASIO.  
  
`boost::asio::io_context::strand` is the old(pre-Networking TS) implementation of a strand. You should use `strand<E>` instead, because the old one copies handlers, IIRC.

---

## Comment 2

> Username: traceon  
> Created at: 2019-01-13 17:46:55 UTC  
> Url: https://github.com/boostorg/asio/issues/195#issuecomment-453850520  

> he problem you're trying to solve seems to be similar to task dependency management, which is out of scope of the executors in ASIO  
  
Looks like yes, but strands can be seen as addressing problems of that same domain...  
  
Thanks for the help!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-01-22 12:40:54 UTC  
> Url: https://github.com/boostorg/asio/issues/195#issuecomment-456385516  

I think you should be able to write something like that yourself, no?

---

## Comment 4

> Username: traceon  
> Created at: 2019-01-22 13:02:09 UTC  
> Url: https://github.com/boostorg/asio/issues/195#issuecomment-456391271  

Yes, naturally, just wanted to make sure I am not inventing the wheel here, and do a reality check of the concept with people who have experience and developed thought culture around asio.  
  
In fact, I am working on an ExecutionContext that acts as a wrapper for multiple io_contexts that run in separate threads/thread pools, and Executor that both provides hierarchy (by allowing to get subordinate Executor instances) and/or fixed thread guaranties, which I was asking about here: https://github.com/boostorg/asio/issues/194  
  
I am concerned with increased locking/sync overhead though, which probably has to be introduced in a solution that is going to be built on top of execution_context/io_context and strands, rather than be a built-in feature of io_context.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-01-22 13:23:15 UTC  
> Url: https://github.com/boostorg/asio/issues/195#issuecomment-456397414  

> In fact, I am working on an ExecutionContext that acts as a wrapper for multiple io_contexts that run in separate threads/thread pools  
  
Is this to support the "multiple-single-threaded-io-contexts" model?  
  
Anyway are you sure you need this? It sounds like a tremendous amount of complexity. I'm not trying to discourage you but rather, to save you work. If you could provide more detail and justification for why you think this construct is necessary, perhaps something might come of it.

---

## Comment 6

> Username: traceon  
> Created at: 2019-01-22 15:25:24 UTC  
> Updated at: 2019-01-22 15:25:34 UTC  
> Url: https://github.com/boostorg/asio/issues/195#issuecomment-456440014  

> Is this to support the "multiple-single-threaded-io-contexts" model?  
  
Yes.  
  
The possible archetypal use case would be: you have a tight stream of messages, that come with some group_id, messages of the same group_id share a common state and are delivered in a same strand, you also have a top level registry of states (group_id -> state map), that owns all the group states. While you can safely modify the state itself, to first access it you have to (shared-)lock the registry and do a lookup, and to remove or add a new group state, you need to write-lock the state.  
  
If only this logic could be offloaded to "hierarchical"/"nested" strands, once you in a "reader" strand, you can lookup the state without any locking, and once you in a "writer" you can modify the registry without locking (assuming "reader" strand is nested in "writer" strand.) Add a proper scheduling to this, assuming writes are much more rare that reads, you will be processing messages without actual read-locking most of the time.

---

## Comment 7

> Username: traceon  
> Created at: 2019-01-23 16:21:43 UTC  
> Url: https://github.com/boostorg/asio/issues/195#issuecomment-456866385  

> Is this to support the "multiple-single-threaded-io-contexts" model?  
  
(in continuation of https://github.com/boostorg/asio/issues/194 ) The master context may simultaneously provide executors of different behaviors: bound to a fixed thread, and bound to a pool of threads, as needed.  
  
Binding an executor to a fixed thread having the following benefits:  
1. (obvious) no contention on (even atomic) locks required for queuing the handlers, etc.  
2. (less obvious) allowing handlers to safely execute code, that is unaware of the execution context's properties, to safely use coroutines (of any implementation) which are allowed to lock mutexes that can be locked/unlocked in other threads too  
3. (even less obvious) ability to bind threads to multiqueue NICs' RX queues to maximize throughput

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-01-23 17:13:59 UTC  
> Url: https://github.com/boostorg/asio/issues/195#issuecomment-456887441  

I'm just a simple programmer, tending to my memes - these use cases are way beyond my knowledge or understanding! (Although, I do get the part about holding a mutex across a context switch)

---

## Comment 9

> Username: ghost  
> Created at: 2020-12-30 00:59:32 UTC  
> Url: https://github.com/boostorg/asio/issues/195#issuecomment-752291100  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#669](https://github.com/chriskohlhoff/asio/issues/669).
