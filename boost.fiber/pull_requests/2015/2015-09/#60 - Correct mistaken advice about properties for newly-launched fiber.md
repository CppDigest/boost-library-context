# #60 Correct mistaken advice about properties for newly-launched fiber. [Merged]

> Username: nat-goodspeed  
> Created at: 2015-09-02 00:52:38 UTC  
> Updated at: 2016-06-06 11:36:34 UTC  
> Merged at: 2015-09-02 06:08:53 UTC  
> Closed at: 2015-09-02 06:08:53 UTC  
> Url: https://github.com/boostorg/fiber/pull/60  

I changed my advice about setting properties.  
  
I also changed `examples/priority.cpp` to reflect my new understanding, converting the `init()` function to a `launch()` function that launches the fiber and immediately sets its properties.  
  
Scrutinizing `priority.cpp` output, I discovered a bug in my `priority_scheduler::property_change()` method. Given a ready queue containing [medium(2), low(1), high(0)], a `property_change()` call with `high` reset to priority 3 would move it here: [medium(2), high(3), low(1)].  
  
That rubbed my nose in the fact that my "clever" single-pass `property_change()` logic was hard to reason about -- bad news for an example program! I decided to convert it to two simpler passes: the first to find and unlink the passed fiber_context*, the second to re-insert it in the correct place. Happily, I then realized that once the fiber_context* is unlinked, I already _have_ a method to reinsert it in the correct place: `awakened()`. Empirically, observing the output, this works -- and is much easier to desk-check.  
  
I'm somewhat curious whether you would consider my original "clever" single-pass logic to be an example of premature optimization. That is, without actually running a profiler, does intuition have anything useful to suggest about the relative performance of a single pass through a linked list, with conditionals, versus two simpler passes?  
  
While paying attention to `priority.cpp` output, I decided to set the name of the main fiber too -- which surfaced another bug, this time in `this_fiber::properties<>()`. The mechanism relies on `sched_algorithm_with_properties::awakened()` to instantiate the `fiber_properties` subclass for each `fiber_context*`. But each thread's main fiber is entered without having passed through the scheduler! Fortunately there's a simple workaround: all you have to do is call `this_fiber::yield()` to detour through the scheduler. I made `this_fiber::properties<>()` invoke that workaround as needed.  
  
This _could_ have unforeseen side effects if your main fiber instantiates some number of `fiber` objects before deciding to set its own properties: the new fibers will run before you perhaps expect them to. I consider that an obscure and unlikely problem. I think if you plan to set the main fiber's properties, you'll probably do it early. The bit about detouring through the scheduler, the requirement to pass through `sched_algorithm_with_properties::awakened()`, is an implementation detail I don't particularly want to have to explain.  
  
What do you think?

---

## Comment 1

> Username: olk  
> Created_at: 2015-09-02 06:18:40 UTC  
> Url: https://github.com/boostorg/fiber/pull/60#issuecomment-136947706  

> This could have unforeseen side effects if your main fiber instantiates some number of fiber objects before deciding to set its own properties: the new fibers will run before you perhaps expect them to. I consider that an obscure and unlikely problem.  
  
I agree - I would consider it as a logical bug. I think we should add a comment regarding to this issue in the documentation!  
  
> The bit about detouring through the scheduler, the requirement to pass through sched_algorithm_with_properties::awakened(), is an implementation detail I don't particularly want to have to explain.  
  
agreed - don't mention it in the documentation

---

## Comment 2

> Username: olk  
> Created_at: 2015-09-02 06:21:19 UTC  
> Url: https://github.com/boostorg/fiber/pull/60#issuecomment-136948034  

> The mechanism relies on sched_algorithm_with_properties::awakened() to instantiate the fiber_properties subclass for each fiber_context*. But each thread's main fiber is entered without having passed through the scheduler! Fortunately there's a simple workaround: all you have to do is call this_fiber::yield() to detour through the scheduler.   
  
The documentation should contain a note that you have to call use_sched_algorith() first and then call   
this_fiber::properties< priority_props >() for the main-fiber.  
Do you agree?

---

## Comment 3

> Username: nat-goodspeed  
> Created_at: 2015-09-02 16:04:59 UTC  
> Url: https://github.com/boostorg/fiber/pull/60#issuecomment-137145416  

So do you think it sufficient if I add a note something like this to this_fiber::properties<>() documentation?  
  
"The first time this function is called from the main fiber of a thread, it may internally yield, permitting other fibers to run."  
  
this_fiber::properties<PROPS>() already states as its Preconditions: use_scheduling_algorithm() has been called from this thread with a subclass of sched_algorithm_with_properties<> with the same template argument PROPS.

---

## Comment 4

> Username: olk  
> Created_at: 2015-09-02 17:33:49 UTC  
> Url: https://github.com/boostorg/fiber/pull/60#issuecomment-137181348  

yes

---
