# #16 - segmentation fault when calling clRetainDevice in boost::compute::device::device(cl_device_id id, bool retain) [Closed]

> Username: ulrichard  
> Created at: 2013-07-04 21:36:53 UTC  
> Updated at: 2013-07-09 22:50:59 UTC  
> Closed at: 2013-07-09 22:03:04 UTC  
> Url: https://github.com/boostorg/compute/issues/16  

On another machine it works, but on my workstation, I get a segmentation fault SIGSEV when calling clRetainDevice in boost::compute::device::device(cl_device_id id, bool retain). Other OpenCL applications work on this machine, but they might not call clRetainDevice().   
  
If I comment out the calls to clRetainDevice() and clReleaseDevice() I can run some examples, while others still segfault :   
$example/hello_world  
 hello from GeForce 9800 GT  
$example/list_devices  
 Platform 'NVIDIA CUDA'  
  GPU Device: GeForce 9800 GT  
$examples/vector_addition  
 SIGSEV  
  
That's what other OpenCL apps report about the device:  
NVIDIA CUDA  
NVIDIA Corporation  
cl_khr_byte_addressable_store cl_khr_icd cl_khr_gl_sharing cl_nv_compiler_options cl_nv_device_attribute_query cl_nv_pragma_unroll   
GeForce 9800 GT  
NVIDIA Corporation  
cl_khr_byte_addressable_store cl_khr_icd cl_khr_gl_sharing cl_nv_compiler_options cl_nv_device_attribute_query cl_nv_pragma_unroll  cl_khr_global_int32_base_atomics cl_khr_global_int32_extended_atomics  
  
That's on ubuntu 13.04

---

## Comment 1

> Username: kylelutz  
> Created at: 2013-07-09 02:07:16 UTC  
> Url: https://github.com/boostorg/compute/issues/16#issuecomment-20649219  

Hi Richard,  
  
I think this is caused by a mismatch in the OpenCL headers you're compiling against and the OpenCL library provided by NVIDIA. The `clRetainDevice()` function is only available in OpenCL 1.2 while NVIDIA's latest SDK only supports OpenCL 1.1.  
  
Can you try setting the `OPENCL_INCLUDE_DIRS` variable in cmake to the path for the OpenCL headers provided by NVIDIA? On my linux system it is `/usr/include/nvidia-current`.  
  
Let me know if this works for you.  
  
Cheers,  
Kyle

---

## Comment 2

> Username: ulrichard  
> Created at: 2013-07-09 22:03:04 UTC  
> Url: https://github.com/boostorg/compute/issues/16#issuecomment-20708852  

Hi Kyle,  
  
indeed, OPENCL_INCLUDE_DIRS was set to /usr/include, and changing it to /usr/include/nvidia-current as it was already set to for another application that I developed half a year ago.  
Changing this in the compute build dir, the examples execute correctly.  
The strange thing about this is that /usr/include/nvidia-current doesn't exist (any more) on this system.  
'''  
$ find /usr/include/ -iname opencl.h   
/usr/include/CL/opencl.h  
'''  
shows that I have only this one instance of OpenCL headers.  
  
And now changing everything back, it still works. No Idea why it works now with the same settings as it failed before. Maybe some relevant package was updated in the meantime.   
  
Thanks for helping anyway, and for that cool library. Any idea when it might become part of boost officially?

---

## Comment 3

> Username: kylelutz  
> Created at: 2013-07-09 22:50:59 UTC  
> Url: https://github.com/boostorg/compute/issues/16#issuecomment-20711103  

Try looking for the `cl.h` header instead.  
  
On my system:  
  
$ find /usr/include/ -iname cl.h   
/usr/include/nvidia-current/CL/cl.h
