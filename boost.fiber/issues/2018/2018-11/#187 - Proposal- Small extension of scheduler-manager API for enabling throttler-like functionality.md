# #187 - Proposal: Small extension of scheduler/manager API for enabling throttler-like functionality [Closed]

> Username: niekbouman  
> Created at: 2018-11-22 10:49:24 UTC  
> Updated at: 2019-01-04 12:59:44 UTC  
> Closed at: 2019-01-04 12:59:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/187  

Dear @olk ,  
  
Thank you for your work on Boost.Fiber.  
  
I would like to make a "throttler" such that my custom `async` function will not spawn if there are already at least _N_ fibers.  
  
I want to implement this by making a custom `async` function that calls `yield()` while the current size of `worker_queue_` is equal to or exceeds _N_, and otherwise creates the fiber just like the `async` function defined in `fiber/future/async.hpp`    
  
This requires a library-user to query the current length of the fiber manager's `worker_queue_`.  
I propose to add the following to the public API of the `scheduler` class declared in `scheduler.hpp`:  
```cpp  
inline size_t num_workers() noexcept { return worker_queue_.size(); }   
```  
  
My questions:  
1) What do you think of this approach?  
2) Would you accept a pull request that adds this change?

---

## Comment 1

> Username: romange  
> Created at: 2019-01-04 11:17:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/187#issuecomment-451417667  

@niekbouman    
  
`yield` does not suspend the calling fiber, it just gives up on active context ownership and it allows the scheduler fiber to choose another active fiber from its queue. Therefore, your `async` function could create a dangerous "busy-wait" loop where no-active fibers are present (with `worker_queue_.size() >= N`) and it continuously yields.  
  
On the other hand, providing potentially blocking `async` is misleading (since async mean non-blocking) and therefore dangerous as well :)  
  
I would create your own "FiberPool" that accepts a function, spawns a fiber and runs it there according to the policy you need. It's enqueing function could block the calling fiber.

---

## Comment 2

> Username: romange  
> Created at: 2019-01-04 11:40:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/187#issuecomment-451421970  

Also see this [similar discussion](https://github.com/boostorg/fiber/issues/163).

---

## Comment 3

> Username: niekbouman  
> Created at: 2019-01-04 12:59:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/187#issuecomment-451437313  

Hi @romange,  
Thank you very much for your comments and pointer to the other discussion.  
  
I already use a "FiberPool"-like approach that blocks in its spawning function. The reason for my suggestion was to avoid keeping track of yet another counter (and add the burden to each spawned fiber to decrease the counter upon completion, meaning another wrapper layer), while this count is already book-kept inside the scheduler.   
However, I agree it makes sense to keep track of the count in the "FiberPool", to distinguish between fibers belonging to the pool and other "unrelated" fibers, which removes the need for changing `scheduler.hpp`.
