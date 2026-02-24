# #283 - Setting fiber_properties on fiber construction [Closed]

> Username: lowsfer  
> Created at: 2021-09-20 15:11:28 UTC  
> Updated at: 2021-10-02 20:38:39 UTC  
> Closed at: 2021-10-02 20:38:39 UTC  
> Url: https://github.com/boostorg/fiber/issues/283  

I'm trying to implement a scheduler supporting both work-stealing and task priority. I don't need to change priority after the first priority setting (ideally on construction). But this is not possible with the current APIs.  
  
Currently, a fiber gets it's property on first awakened call. So either I need to use launch::post, or yield in the fiber first. But in ready state, the fiber may be stolen by another thread, so the call to set_priority() need to handle thread-safety issues. This seems to be too complex and probably inefficient.  
  
There are also other issues which can be easily solved with this, e.g. https://github.com/boostorg/fiber/issues/150  
  
If fiber_properties can be assigned inside fiber and context constructor, it would be much simpler. I don't even need to handle property change.  
  
Because fiber_properties ctor takes a context*, we probably need to:  
1. either pass a fiber_properties creator functor into fiber/context ctor,  
2. or allow context::set_property to modify fiber_properties::ctx_. So fiber_properties::set_context() is required, or make context a friend of fiber_properties.  
  
I'm willing to make the change if it makes sense to you and there is no other volunteers.  
  
I can keep it fully API/ABI compatible with existing user code. But I wonder if that's necessary for some APIs like make_worker_context() and worker_context ctor.  
  
Please give suggestions. Thanks.

---

## Comment 1

> Username: olk  
> Created at: 2021-09-20 17:00:49 UTC  
> Updated at: 2021-09-20 17:01:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/283#issuecomment-923108475  

I would opt for solution 2... could you provide a PR

---

## Comment 2

> Username: lowsfer  
> Created at: 2021-09-21 01:38:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/283#issuecomment-923507179  

Please see https://github.com/boostorg/fiber/pull/284

---

## Comment 3

> Username: lowsfer  
> Created at: 2021-09-22 09:49:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/283#issuecomment-924766361  

I have successfully implemented what I need (a scheduler supporting both work-stealing and priority) based on https://github.com/boostorg/fiber/pull/284  
  
Hope it can be merged so I can use system boost headers in the future.
