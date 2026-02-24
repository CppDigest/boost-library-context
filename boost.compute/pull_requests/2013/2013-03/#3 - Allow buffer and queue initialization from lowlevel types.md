# #3 Allow buffer and queue initialization from lowlevel types [Merged]

> Username: ddemidov  
> Created at: 2013-03-19 20:53:41 UTC  
> Updated at: 2013-06-24 12:07:35 UTC  
> Merged at: 2013-03-19 21:22:46 UTC  
> Closed at: 2013-03-19 21:22:46 UTC  
> Url: https://github.com/boostorg/compute/pull/3  

This commit adds compute::command_queue constructor from cl_command_queue and makes compute::buffer(cl_mem) constructor public.  
  
This would allow to use Boost.Compute algorithms from externally initialized OpenCL contexts.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2013-03-19 21:23:28 UTC  
> Url: https://github.com/boostorg/compute/pull/3#issuecomment-15144071  

Merged. Thanks for the patch!

---
