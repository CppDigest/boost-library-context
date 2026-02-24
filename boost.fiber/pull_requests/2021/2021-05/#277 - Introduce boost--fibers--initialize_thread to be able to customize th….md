# #277 [RFC] Introduce boost::fibers::initialize_thread to be able to customize th… [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2021-05-11 20:13:17 UTC  
> Updated at: 2023-10-01 15:08:09 UTC  
> Merged at: 2023-10-01 15:08:04 UTC  
> Closed at: 2023-10-01 15:08:04 UTC  
> Url: https://github.com/boostorg/fiber/pull/277  

…e dispatching coroutine stack allocator.  
  
Hi @olk,  
  
Please note that this pull request in its initial shape is obviously of very little quality (for example for now I have not checked that it builds (yet, but I will do some test locally), however I wish to know first if the new API being added and the idea of this change is ok or not.  
  
I wish to be able to control not only the scheduling algorithm, but also the stack allocation of the dispatching coroutine, for which now is hardcoded and can't be changed. My motivation is that we do want to be able to control whether or not the stack are being explicitly registered with valgrind, and unfortunately the current code enabling/disabling valgrind is not binary compatible, and forces each users to know if boost::fiber was build or not with valgrind support mode and define a macro explicitly, which isn't quite user friendly at scale. So instead we do have custom allocator registering the valgrind stack when we want it. Note that maybe other people might want to customize the stack allocation of the dispatching coroutine (to pool them for example if they often start/stop threads in addition to using coroutines).  
  
The only purpose of stack_allocator_wrapper is to make the allocator polymorphic: "capture" the user provided stack allocator logic and inject it into libboost_fiber.so which doesn't know the user stack allocator type at build time. I don't know if that type shall rather be directly in boost::context instead.  
  
Note that I am unsure which coding style shall be used. I have not updated any doc right now since I am unsure this would be accepted like this.  
  
Please guide me in the right direction to be able to customize this specific stack allocator ;)  
  
Cheers,  
Romain

---

## Comment 1

> Username: olk  
> Created_at: 2021-05-12 05:47:55 UTC  
> Url: https://github.com/boostorg/fiber/pull/277#issuecomment-839467301  

your changes make sense, please go ahead ...

---

## Comment 2

> Username: olk  
> Created_at: 2023-10-01 15:08:08 UTC  
> Url: https://github.com/boostorg/fiber/pull/277#issuecomment-1742110094  

ty

---
