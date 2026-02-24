# #124 - Assert bug in enqueue_nd_range_kernel [Closed]

> Username: Mageswaran1989  
> Created at: 2014-05-28 11:52:36 UTC  
> Updated at: 2014-06-05 02:11:12 UTC  
> Closed at: 2014-06-05 02:10:59 UTC  
> Url: https://github.com/boostorg/compute/issues/124  

File          :  command_queue.hpp  
Line 1077:  BOOST_ASSERT(work_dim > 0 && work_dim <= CL_DEVICE_MAX_WORK_ITEM_DIMENSIONS);  
Aren't we suppose to query the device for max work dimensions?  
Is this planned to take take care in future?

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-29 06:35:38 UTC  
> Updated at: 2014-05-29 06:35:48 UTC  
> Url: https://github.com/boostorg/compute/issues/124#issuecomment-44499004  

Wow. Yup, that's a bug. I'll remove it.  
  
Thanks for reporting this!

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-06-05 02:10:59 UTC  
> Updated at: 2014-06-05 02:11:12 UTC  
> Url: https://github.com/boostorg/compute/issues/124#issuecomment-45174039  

Removed the assert, the commit is here: 07ae8638dea5c54100e69b496191b0978a0751b3.
