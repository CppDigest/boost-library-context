# #530 - CL_MAP_WRITE_INVALIDATE_REGION is a valid flag in 1.2 [Closed]

> Username: pisto  
> Created at: 2015-11-04 20:54:40 UTC  
> Updated at: 2015-11-11 05:05:56 UTC  
> Closed at: 2015-11-11 05:05:55 UTC  
> Url: https://github.com/boostorg/compute/issues/530  

No need for this ifdef https://github.com/boostorg/compute/blob/master/include/boost/compute/command_queue.hpp#L88

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-11-04 21:00:33 UTC  
> Url: https://github.com/boostorg/compute/issues/530#issuecomment-153863849  

Based on this https://www.khronos.org/registry/cl/sdk/1.1/docs/man/xhtml/enums.html#cl_map_flags and this https://www.khronos.org/registry/cl/sdk/1.2/docs/man/xhtml/enums.html#cl_map_flags, I think ifdef is needed, but it should be `CL_VERSION_1_2` instead of `CL_VERSION_2_0`.

---

## Comment 2

> Username: pisto  
> Created at: 2015-11-04 21:01:05 UTC  
> Url: https://github.com/boostorg/compute/issues/530#issuecomment-153863979  

Yeah.

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-11-04 21:02:12 UTC  
> Url: https://github.com/boostorg/compute/issues/530#issuecomment-153864239  

Thanks ;) I'll fix it soon.

---

## Comment 4

> Username: jszuppe  
> Created at: 2015-11-07 17:51:02 UTC  
> Url: https://github.com/boostorg/compute/issues/530#issuecomment-154729942  

Can be closed - see https://github.com/boostorg/compute/pull/533.
