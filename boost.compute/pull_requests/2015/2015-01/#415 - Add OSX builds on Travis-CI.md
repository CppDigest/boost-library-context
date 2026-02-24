# #415 Add OSX builds on Travis-CI [Closed]

> Username: roshanrags  
> Created at: 2015-01-17 21:35:31 UTC  
> Updated at: 2016-05-13 02:54:56 UTC  
> Closed at: 2016-05-13 02:54:56 UTC  
> Url: https://github.com/boostorg/compute/pull/415  

This adds OS X builds on Travis-CI.  
  
It takes way too long to install dependencies but I'm not sure what can be done about it...

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-01-21 04:04:57 UTC  
> Url: https://github.com/boostorg/compute/pull/415#issuecomment-70781429  

Awesome! And I don't think the time issue is too big of a deal.  
  
One small recommendation, could you change the name of the install script from `before_install.sh` to something like `.travis_install.sh`? I wouldn't want end-users thinking they have to run this script in order to install Boost.Compute itself.  
  
And the test errors are something we'll have to fix before actually merging this (unless there is some way to temporarily disable testing on just OS X). The core issue is that on CPU-like devices on Apple platforms, any use of `__local` memory will cause the kernel to report that it only supports a maximum of `1` work-item and thus the `clEnqueueNDRangeKernel()` fails when we use it with algorithms designed for multiple work-items. I think the fix would to be to have separate implementations of these core algorithms (like `copy()`, `sort()`, and `reduce()`) which will dispatch to non-local-memory based algorithms by checking whether or not the device actually supports `__local` memory (which can be checked with `device.get_info<CL_DEVICE_LOCAL_MEM_TYPE>() == CL_LOCAL`).

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-10-03 21:23:19 UTC  
> Url: https://github.com/boostorg/compute/pull/415#issuecomment-145291826  

We should test it again.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-05-11 09:31:50 UTC  
> Url: https://github.com/boostorg/compute/pull/415#issuecomment-218408911  

This can be closed.

---
