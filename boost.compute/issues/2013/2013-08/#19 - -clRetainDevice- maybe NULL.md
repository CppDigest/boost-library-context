# #19 - "clRetainDevice" maybe NULL [Closed]

> Username: webbery  
> Created at: 2013-08-19 01:40:51 UTC  
> Updated at: 2014-05-07 04:54:33 UTC  
> Closed at: 2014-05-07 04:51:15 UTC  
> Url: https://github.com/boostorg/compute/issues/19  

I have an Intel OpenCL which support OpenCL1.2 and NVidia which support OpenCL1.0 on my compute. I'm always getting an error when the exmaple "hello world" runs in clRetainDevice.  
I used the lib of Intel OpenCL.

---

## Comment 1

> Username: kylelutz  
> Created at: 2013-08-20 12:47:39 UTC  
> Url: https://github.com/boostorg/compute/issues/19#issuecomment-22942258  

The library will use functions based on the OpenCL version reported by the `CL/cl.h` header you compile against. In order to properly run against NVIDIA's OpenCL, you should set your header include paths to point to the `CL/cl.h` header provided by NVIDIA (mine is in `/usr/include/nvidia-current/`). Same goes for compiling against Intel's OpenCL (which is `/opt/intel/opencl-1.2-3.0.56860/include/` for me).  
  
Let me know if this doesn't work.  
  
Cheers,  
Kyle

---

## Comment 2

> Username: webbery  
> Created at: 2013-08-21 02:13:28 UTC  
> Url: https://github.com/boostorg/compute/issues/19#issuecomment-22991368  

The capability of my computer reported by Intel OpenCL "capsbasic" is:  
  
Number of available platforms: 2  
Platform names:  
    [0] NVIDIA CUDA  
    [1] Intel(R) OpenCL [Selected]  
Number of devices available for each type:  
    CL_DEVICE_TYPE_CPU: 1  
    CL_DEVICE_TYPE_GPU: 0  
    CL_DEVICE_TYPE_ACCELERATOR: 0  
  
**\* Detailed information for each device ***  
  
CL_DEVICE_TYPE_CPU[0]  
    CL_DEVICE_NAME:         Intel(R) Core(TM) i7-2600 CPU @ 3.40GHz  
    CL_DEVICE_AVAILABLE: 1  
    CL_DEVICE_VENDOR: Intel(R) Corporation  
    CL_DEVICE_PROFILE: FULL_PROFILE  
    CL_DEVICE_VERSION: OpenCL 1.2 (Build 63463)  
    CL_DRIVER_VERSION: 1.2  
    CL_DEVICE_OPENCL_C_VERSION: OpenCL C 1.2   
  
---  
  
Number of available platforms: 2  
Platform names:  
    [0] NVIDIA CUDA [Selected]  
    [1] Intel(R) OpenCL  
Number of devices available for each type:  
    CL_DEVICE_TYPE_CPU: 0  
    CL_DEVICE_TYPE_GPU: 1  
    CL_DEVICE_TYPE_ACCELERATOR: 0  
  
**\* Detailed information for each device ***  
  
CL_DEVICE_TYPE_GPU[0]  
    CL_DEVICE_NAME: GeForce 505  
    CL_DEVICE_AVAILABLE: 1  
    CL_DEVICE_VENDOR: NVIDIA Corporation  
    CL_DEVICE_PROFILE: FULL_PROFILE  
    CL_DEVICE_VERSION: OpenCL 1.0 CUDA  
    CL_DRIVER_VERSION: 286.10  
    CL_DEVICE_OPENCL_C_VERSION: OpenCL C 1.1   
  
Therefore I have to write the code to get the second platform and pass it to compute::device . But I think a OpenCL library can find different version of different platform. It may be 1.0 or latest. So what's the way to solve it?

---

## Comment 3

> Username: kylelutz  
> Created at: 2013-08-22 01:16:45 UTC  
> Url: https://github.com/boostorg/compute/issues/19#issuecomment-23062585  

Interesting. I'll have to thing more on how to support devices from two different platforms with two different OpenCL versions simultaneously. For now, can you try compiling against the OpenCL version 1.1 headers? That should serve as the "lowest common denominator" and allow you to use both devices.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-05-06 01:54:25 UTC  
> Url: https://github.com/boostorg/compute/issues/19#issuecomment-42260472  

So I ran into this problem again. I think I have a fix here (54c2ab3685538cf560db38c2667995566549fa49). Could you try it out and let me know if it works for you?

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-05-07 04:51:15 UTC  
> Updated at: 2014-05-07 04:54:33 UTC  
> Url: https://github.com/boostorg/compute/issues/19#issuecomment-42389311  

Fixed in 88b6a8b3d4863589d2730e3dd1eed3103829ec0f.
