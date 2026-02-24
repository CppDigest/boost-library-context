# #541 - OpenCL 2.0 atomic functions, program-scope global variables [Closed]

> Username: jszuppe  
> Created at: 2015-12-08 15:21:57 UTC  
> Updated at: 2017-04-24 16:44:50 UTC  
> Closed at: 2017-04-24 16:44:50 UTC  
> Url: https://github.com/boostorg/compute/issues/541  

I think there are no wrapper functions for new OpenCL 2.0 atomics, so that's a "todo".  
  
There's also a problem that when OpenCL 2.0 features are used option "-cl-std=CL2.0" must be included in program build options, so user must be aware of using OpenCL 2.0. Maybe it should be mentioned somewhere in the documentation? Maybe there should be a option to force compilation of Boost.Compute algorithms and/or user kernels in OpenCL 1.0, 1.1, 1.2 or 2.0.   
  
See:  
- https://www.khronos.org/registry/cl/sdk/2.0/docs/man/xhtml/ (OpenCL Compiler > Built-in Functions > Atomic Functions)  
- https://software.intel.com/en-us/articles/using-opencl-20-atomics  
- http://developer.amd.com/community/blog/2015/01/15/opencl-2-0-fine-grain-shared-virtual-memory/  
  
Opinions?  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-12-10 22:19:26 UTC  
> Url: https://github.com/boostorg/compute/issues/541#issuecomment-163765976  

Yeah, it would be good to use these new features when available. Taking advantage of some of them may involve using different algorithms which are then selected at run-time for compatible devices.  
  
As for the compilation flags, it may make sense to always compile kernels at the highest OpenCL version supported by the device (from `CL_DEVICE_OPENCL_C_VERSION`).

---

## Comment 2

> Username: keryell  
> Created at: 2015-12-11 11:07:16 UTC  
> Url: https://github.com/boostorg/compute/issues/541#issuecomment-163911515  

On some platforms, selecting the language version (1.2 against 2.0) has an impact on the compiler/driver path used behind the scene. So that means that for performance issues or bug avoidance, you may end up selecting between 1.2 and 2.0 just for that and not always going for the latest version, sadly...
