# #46 Eliminate opaque fiber_properties::back_ptr typedef. [Merged]

> Username: nat-goodspeed  
> Created at: 2015-08-14 20:13:10 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2015-08-14 20:43:35 UTC  
> Closed at: 2015-08-14 20:43:35 UTC  
> Url: https://github.com/boostorg/fiber/pull/46  

It's more straightforward to use the underlying type: fiber_context*.

---
