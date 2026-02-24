# #504 - Unavoidable deprecated warnings [Closed]

> Username: pisto  
> Created at: 2015-09-08 13:30:23 UTC  
> Updated at: 2016-04-30 03:13:39 UTC  
> Closed at: 2016-04-30 03:13:34 UTC  
> Url: https://github.com/boostorg/compute/issues/504  

When compiling the simple test programs found in the wiki, I get spammed by the following warning:  
  
```  
/usr/local/include/compute/boost/compute/command_queue.hpp:1219:19: warning: ‘cl_int clEnqueueMarker(cl_command_queue, _cl_event**)’ is deprecated [-Wdeprecated-declarations]  
             ret = clEnqueueMarker(m_queue, &event_.get());  
                   ^  
```  
  
That function is not used, in fact `clEnqueueMarker` is not even found in the imports of the output binary. I believe you should define CL_USE_DEPRECATED_OPENCL_1_1_APIS in the headers somewhere.

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-09-08 14:36:36 UTC  
> Url: https://github.com/boostorg/compute/issues/504#issuecomment-138584909  

Yeah, it's because we need to check device's OpenCL version during the runtime. Otherwise, if somebody includes OpenCL 1.2 headers and device supports only 1.1 version, the wrong function would be called.  
  
I think defining `CL_USE_DEPRECATED_OPENCL_1_1_APIS` (and also `*_1_0_APIS`, `*_1_2_APIS`) in CMakeLists.txt files for tests, examples and benchmarks is not a bad idea (not in the main CMakeLists.txt). Option in main CMakeLists.txt can be add for this (with default ON).

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-03-10 22:00:36 UTC  
> Url: https://github.com/boostorg/compute/issues/504#issuecomment-195071609  

It can be close, it's solved by PR https://github.com/boostorg/compute/pull/566.

---

## Comment 3

> Username: kylelutz  
> Created at: 2016-04-30 03:13:34 UTC  
> Url: https://github.com/boostorg/compute/issues/504#issuecomment-215928524  

Closing, please re-open if you see more deprecated warnings.
