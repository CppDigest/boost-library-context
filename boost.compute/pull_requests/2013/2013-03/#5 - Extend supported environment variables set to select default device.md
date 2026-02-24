# #5 Extend supported environment variables set to select default device [Closed]

> Username: ddemidov  
> Created at: 2013-03-26 08:39:09 UTC  
> Updated at: 2013-06-24 12:07:36 UTC  
> Closed at: 2013-04-13 01:06:30 UTC  
> Url: https://github.com/boostorg/compute/pull/5  

With this patch, boost::compute::system::default_device() supports the following environment  
variables:  
- `BOOST_COMPUTE_DEFAULT_DEVICE` for device name;  
- `BOOST_COMPUTE_DEFAULT_PLATFORM` for OpenCL platform name;  
- `BOOST_COMPUTE_DEFAULT_VENDOR` for device vendor name.  
1. If one or more of these variables is set, then device that satisfies all  
   conditions gets selected.  
2. If such a device is unavailable, then the first available GPU is selected.  
3. If there are no GPUs in the system, then the first available CPU is selected.  
4. Otherwise, default_device() returns null device.  
  
The hello_world example is modified to use default_device() instead of  
default_gpu_device().  
  
(edited from:   
BOOST_COMPUTE_DEFAULT_DEVICE="Intel"  
is much more convenient and less error prone than  
BOOST_COMPUTE_DEFAULT_DEVICE="Intel(R) Core(TM) i7 CPU 920 @ 2.67GHz"  
)

---

## Comment 1

> Username: kylelutz  
> Created_at: 2013-04-12 00:30:10 UTC  
> Url: https://github.com/boostorg/compute/pull/5#issuecomment-16269953  

Hey Denis,  
  
Sorry it took me so long to get around to this. I thought about it a bit and I like the idea but I have a few modifications. One problem I see is that the while the name for Intel devices contains "Intel", the name for NVIDIA GPU's don't (one of mine returns "NVS 4200M").  
  
I think a better approach would be to add an additional environmental variable, `BOOST_COMPUTE_DEFAULT_VENDOR`, and then use the following procedure in `default_device()`:  
1. If `BOOST_COMPUTE_DEFAULT_DEVICE` is specified, return the first device whose name matches exactly.  
2. If `BOOST_COMPUTE_DEFAULT_VENDOR` is specified, return the first device whose vendor's name matches exactly.  
3. Return the first GPU device found.  
4. Return the first CPU device found.  
  
Another possibility is to have a `BOOST_COMPUTE_DEFAULT_PLATFORM` and then the first device on a platform with the given name would be used.  
  
Let me know what you think.  
  
-kyle

---

## Comment 2

> Username: ddemidov  
> Created_at: 2013-04-12 03:44:08 UTC  
> Url: https://github.com/boostorg/compute/pull/5#issuecomment-16274507  

Hello Kyle,  
  
I like the idea about more environment variables, but I still think that specifying substrings is more convenient and could potentially save the world from repetitive strain injury :). In my example I could also specify `BOOST_COMPUTE_DEFAULT_DEVICE="920"`, and you could shorten "NVS 4200M" to "NVS" or "4200M".   
  
In VexCL I have a set of predefined [device filters](http://ddemidov.github.io/vexcl/namespacevex_1_1Filter.html) and my personal favorite is [the one](https://github.com/ddemidov/vexcl/blob/master/vexcl/devlist.hpp#L172) based on environment variables. The two variables I tend to use the most are OCL_DEVICE and OCL_PLATFORM. The platform filter is more convenient than vendor for me, because I have quite a zoo on my machine:  
  
```  
  Intel(R) Core(TM) i7 CPU         920  @ 2.67GHz  
    CL_PLATFORM_NAME              = AMD Accelerated Parallel Processing  
  
  Intel(R) Core(TM) i7 CPU         920  @ 2.67GHz  
    CL_PLATFORM_NAME              = Intel(R) OpenCL  
  
  Tesla K20c  
    CL_PLATFORM_NAME              = NVIDIA CUDA  
  
  GeForce 9800 GT  
    CL_PLATFORM_NAME              = NVIDIA CUDA  
  
  Tesla C2075  
    CL_PLATFORM_NAME              = NVIDIA CUDA  
```  
  
Vendor filter would not be very useful, because `Intel` would give me two copies of the same CPU, and 'NVIDIA' would return the three GPUs. On the other hand `OCL_PLATFORM="AMD"` or `"Intel"` would select the right implementation for me (again, the use of shortened names saves my day). But I agree that filtering by vendor is sometimes convenient.  
  
I'll change my patch to support the three environment variables.  
  
##   
  
Cheers,  
Denis

---

## Comment 3

> Username: ddemidov  
> Created_at: 2013-04-12 06:41:55 UTC  
> Url: https://github.com/boostorg/compute/pull/5#issuecomment-16278129  

Kyle, take a look at 160089e64f7fd4efbbdbe0ac56c1f1d9bf9d90e2. Feel free to change the [matches()](https://github.com/ddemidov/compute/blob/160089e64f7fd4efbbdbe0ac56c1f1d9bf9d90e2/include/boost/compute/system.hpp#L160) function to only return `true` in case of exact match.  
  
The [device_platform()](https://github.com/ddemidov/compute/blob/160089e64f7fd4efbbdbe0ac56c1f1d9bf9d90e2/include/boost/compute/system.hpp#L156) function could in principle be made a `boost::compute::device` method, but that creates circular dependency between device.hpp and platform.hpp.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2013-04-13 01:06:30 UTC  
> Url: https://github.com/boostorg/compute/pull/5#issuecomment-16324978  

Looks good!  
  
I've squashed the last two commits together and cherry-picked it into my branch here: 8b78d4187dd468b71ffa29b7bdd8039ae31f41f1.  
  
Thanks!

---
