# #438 - Crash calling clCreateCommandQueueWithProperties [Closed]

> Username: rolandhumphries  
> Created at: 2015-03-21 01:24:51 UTC  
> Updated at: 2017-04-24 16:09:17 UTC  
> Closed at: 2017-04-24 16:09:17 UTC  
> Url: https://github.com/boostorg/compute/issues/438  

The crash is being caused because I was linking against a V2.0 OpenCL library but I don't currently have a V2.0 device (I7 w/HD4000 only v1.2) so when it calls the function it just bombs out while trying to create the command queue (win32). It might be worth putting an exception or something that checks when you try to create the context if you've compiled against an incompatible version. It didn't take me long to work it out and I guess production code should be checking the device version before creating the context / queue anyway.  
The solutions to fix it weren't all that elegant though, I could either 1. comment out the #define CL_VERSION_2_0 in cl.h, 2. install the v1.2 libraries or 3. include cl.h before everything else and undef CL_VERSION_2_0 after including it.  
Maybe there could be a better way to select the version you want to compile against in the config.hpp or similar, like BOOST_COMPUTE_TARGET_OPENCL_2_0 or something so you could always use the latest version of the cl headers but specifically target the version you need.  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-03-21 17:32:19 UTC  
> Url: https://github.com/boostorg/compute/issues/438#issuecomment-84405816  

Yeah, the current best way to accomplish this is to only install/compile against the OpenCL headers you want to target (option 2). I'll work on better ways to do this. I like the idea of something like a `BOOST_COMPUTE_TARGET_OPENCL_2_0` macro, let me think about how best to implement this.

---

## Comment 2

> Username: rolandhumphries  
> Created at: 2015-03-22 00:30:16 UTC  
> Url: https://github.com/boostorg/compute/issues/438#issuecomment-84482016  

I did something ugly / basic in cl.hpp just to make everything work with that define, it does what's expected without having to modify every file but I'm sure it could be done better and probably doesn't fit in with boost style / standard anyway but I thought I'd put it up in case it's useful.  
  
```  
#ifndef BOOST_COMPUTE_CL_HPP  
#define BOOST_COMPUTE_CL_HPP  
// remove deprecation warnings when targeting earlier versions of the library  
#if defined(BOOST_COMPUTE_TARGET_OPENCL)  
    #if BOOST_COMPUTE_TARGET_OPENCL < 0x01010000  
        #define CL_USE_DEPRECATED_OPENCL_1_1_APIS  
    #endif // BOOST_COMPUTE_TARGET_OPENCL_1_2  
    #if BOOST_COMPUTE_TARGET_OPENCL < 0x01020000  
        #define CL_USE_DEPRECATED_OPENCL_1_2_APIS  
    #endif // BOOST_COMPUTE_TARGET_OPENCL_1_2  
    #if BOOST_COMPUTE_TARGET_OPENCL < 0x02000000  
        #define CL_USE_DEPRECATED_OPENCL_2_0_APIS  
    #endif // BOOST_COMPUTE_TARGET_OPENCL_1_2  
#endif // defined(BOOST_COMPUTE_TARGET_OPENCL)  
  
#if defined(__APPLE__)  
#include <OpenCL/cl.h>  
#else  
#include <CL/cl.h>  
#endif  
  
// automatically determine the target version if the default is required  
#if !defined(BOOST_COMPUTE_TARGET_OPENCL)  
    #if defined(CL_VERSION_2_0)  
        #define BOOST_COMPUTE_TARGET_OPENCL 0x02000000  
    #elif defined(CL_VERSION_1_2)  
        #define BOOST_COMPUTE_TARGET_OPENCL 0x01020000  
    #elif defined(CL_VERSION_1_1)  
        #define BOOST_COMPUTE_TARGET_OPENCL 0x01010000  
    #elif defined(CL_VERSION_1_0)  
        #define BOOST_COMPUTE_TARGET_OPENCL 0x01000000  
    #else  
        #error BOOST COMPUTE:"No valid CL version available"  
    #endif //   
#endif // !defined(BOOST_COMPUTE_TARGET_OPENCL)  
  
// this prevents the compute code from using the later version features but the compute library could instead use the target version define instead of the CL_VERSION_...  
#if BOOST_COMPUTE_TARGET_OPENCL < 0x01010000  
    #undef CL_VERSION_1_1  
#endif // BOOST_COMPUTE_TARGET_OPENCL_1_2  
#if BOOST_COMPUTE_TARGET_OPENCL < 0x01020000  
    #undef CL_VERSION_1_2  
#endif // BOOST_COMPUTE_TARGET_OPENCL_1_2  
#if BOOST_COMPUTE_TARGET_OPENCL < 0x02000000  
    #undef CL_VERSION_2_0  
#endif // BOOST_COMPUTE_TARGET_OPENCL_1_2  
  
#endif // BOOST_COMPUTE_CL_HPP  
```

---

## Comment 3

> Username: GuSuku  
> Created at: 2015-06-09 23:28:31 UTC  
> Updated at: 2015-06-09 23:31:51 UTC  
> Url: https://github.com/boostorg/compute/issues/438#issuecomment-110532051  

I have [OpenCL 1.2 libraries through Intel SDK for Ubuntu](https://software.intel.com/en-us/articles/intel-code-builder-for-opencl-api), and yet my program crashes upon calling clCreateCommandQueueWithProperties in command_queue.hpp  
  
What worked for me is OP's option 3 (besides option 1) above by including the following before everything else:  
  
```  
#include <CL/cl.h>  
#undef CL_VERSION_2_0  
```  
  
My system info:  
  
```  
-----------------------------------------------------------------------------------------------------------------------  
  
Boost-Compute version: boost-compute 0.4-0ubuntu1  
  
OS name: Linux   
Release:3.13.0-53-generic   
Version:#89-Ubuntu SMP Wed May 20 10:34:39 UTC 2015   
Machine:x86_64   
  
Platform name = Intel(R) OpenCL   
Platform version = OpenCL 1.2 LINUX   
Platform extensions = cl_khr_icd cl_khr_global_int32_base_atomics cl_khr_global_int32_extended_atomics cl_khr_local_int32_base_atomics cl_khr_local_int32_extended_atomics cl_khr_byte_addressable_store cl_khr_depth_images cl_khr_3d_image_writes cl_intel_exec_by_local_thread cl_khr_spir    
  
Device name =        Intel(R) Core(TM) i7-2620M CPU @ 2.70GHz   
Device version = OpenCL 1.2 (Build 43)   
Device global memory size= 3156189184  
Device available? Yes   
Device extensions= cl_khr_icd cl_khr_global_int32_base_atomics cl_khr_global_int32_extended_atomics cl_khr_local_int32_base_atomics cl_khr_local_int32_extended_atomics cl_khr_byte_addressable_store cl_khr_depth_images cl_khr_3d_image_writes cl_intel_exec_by_local_thread cl_khr_spir    
  
Double precision not supported    
  
--------------------------------------------------------------------------------------------------------------------------  
```

---

## Comment 4

> Username: kylelutz  
> Created at: 2015-06-11 04:19:06 UTC  
> Url: https://github.com/boostorg/compute/issues/438#issuecomment-110995174  

@GuSuku This should be fixed in PR #443, can you test that out?

---

## Comment 5

> Username: kylelutz  
> Created at: 2015-06-24 05:08:53 UTC  
> Url: https://github.com/boostorg/compute/issues/438#issuecomment-114728935  

The fix is now in the `develop` branch, could you try that and see if it solves this issue? Thanks!

---

## Comment 6

> Username: GuSuku  
> Created at: 2015-07-01 23:43:42 UTC  
> Updated at: 2015-07-01 23:48:49 UTC  
> Url: https://github.com/boostorg/compute/issues/438#issuecomment-117854033  

Sorry for the late reply.  
  
The 'develop' version doesn't work for me. Gives the same error. I still have to manually undef CL_VERSION_2_0   
  
And thanks!

---

## Comment 7

> Username: jszuppe  
> Created at: 2017-04-24 16:09:17 UTC  
> Url: https://github.com/boostorg/compute/issues/438#issuecomment-296722498  

https://github.com/boostorg/compute/pull/699 should fix it - just define `BOOST_COMPUTE_MAX_CL_VERSION=102` when compiling. Please reopen if it's still a problem.
