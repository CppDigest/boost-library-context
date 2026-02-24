# #323 - Explicit context switch of fibers [Open]

> Username: SerenGTI  
> Created at: 2024-07-26 19:08:29 UTC  
> Updated at: 2024-07-26 19:08:29 UTC  
> Url: https://github.com/boostorg/fiber/issues/323  

Hi there,  
I am currently researching the behavior of non-blocking code such as boost::fiber under different scheduling mechanisms. One such mechanism is the symmetric transfer, wherein one fiber is suspended and another fiber is immediately resumed with no caller/callee relationship between the two.  
  
Hence, I would like to explicitly resume a previously suspended fiber in the current thread, *bypassing the scheduler*. Assume I have a list of suspended fibers, represented by their contexts, for example as  
```c++  
std::deque<context*> suspended_contexts;  
```  
Now, in a running fiber, let's call it `F`, I would like to be able to yield `F` and then resume `suspended_contexts.front()` without passing control to any other fibers that may be in the scheduler's ready queue. However, I seem to be unable to get `context->attach()`, `context->resume()` or any other method used throughout the codebase to work.  
  
I know that the scheduler realizes a context switch between fibers through  
```c++  
// resume another context  
algo_->pick_next()->resume();  
```  
which essentially does  
```c++  
context::active()->attach(next_fiber_context);  
next_fiber_context->resume();  
```  
however, when I use `next_fiber_context = suspended_contexts.front()`, my code deadlocks on the call to `resume()`.  
  
Am I missing something crucial about the fiber context switch?  
Could you direct me to a more detailed documentation of a context switch as it is used in the library?  
  
Moreover, is there a way to pass the suspended fiber `F` to the scheduler so that another thread may execute it in the meantime? A fiber must be explicitly enqueued into the scheduler's ready queue by calling `awakened(ctx)`. Is it safe to do so with `ctx = context::active()`?
