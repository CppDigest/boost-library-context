# #528 - command_queue::enqueue_svm_memcpy(..) does not work [Open]

> Username: jszuppe  
> Created at: 2015-10-24 18:08:23 UTC  
> Updated at: 2017-03-22 17:41:05 UTC  
> Url: https://github.com/boostorg/compute/issues/528  

Hi,  
  
Tests `fill_svm_buffer`, `copy_svm_ptr` and `sum_svm_kernel` fail on my AMD Radeon R9 380. I managed to figure out that is all because of `command_queue::enqueue_svm_memcpy(..)` not working. It does not copy the data. When map and unmap operations are used instead of memcpy, everything works.  
  
It looks like AMD drivers has a bug in `clEnqueueSVMMemcpy()`, but I'll have to test it without Boost.Compute. When it's confirmed, I'll do workaround for this (or we'll just wait for fix).   
  
For now I'll push pull request fixing `fill_svm_buffer` and `sum_svm_kernel`, because in those cases copy operations are just a part of preparations for testing (not tested algorithm) and also because not working `command_queue::enqueue_svm_memcpy(..)` is not the only bug in `sum_svm_kernel` test.  
  
Environment:   
  
```  
OS: Linux Mint 17.2 (3.16)  
Platform Version: OpenCL 2.0 AMD-APP (1800.11)  
Board name: AMD Radeon (TM) R9 380 Series   
Device OpenCL C version: OpenCL C 2.0   
AMD GPU Drivers: 15.9 (15.201.1151)  
```  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-10-27 02:39:01 UTC  
> Url: https://github.com/boostorg/compute/issues/528#issuecomment-151350446  

Hmm, that is frustrating. IIRC, `clEnqueueSVMMemcpy()` worked fine for me with both AMD and Intel OpenCL 2.0 drivers. What happens on your machine, just no data is ever copied? One interesting thing to check is whether copying different amounts of data takes different amounts of time. This could let us know if it's simply unimplemented (if it always just returns and takes essentially zero time) or if it is doing something, just not correctly.  
  
In any case, it would probably be good to add this to `test/quirks.hpp` and still keep a test or two for `enqueue_svm_memcpy()` around for devices which support it correctly.

---

## Comment 2

> Username: jszuppe  
> Created at: 2015-10-27 08:26:35 UTC  
> Updated at: 2015-10-27 08:36:21 UTC  
> Url: https://github.com/boostorg/compute/issues/528#issuecomment-151412408  

I opened a discussion on AMD forum - https://community.amd.com/message/2680309#2680309, but so far nobody has answered me. I attached short program that tests `clEnqueueSVMMemcpy()`.  
  
OK, I tested the execution time. If event is assigned to  `clEnqueueSVMMemcpy()` operation `clGetEventProfilingInfo()` returns error `CL_PROFILING_INFO_NOT_AVAILABLE -7`. After this I tried measuring time with `std::chrono` I always get 0 ms.  
  
Just for comparison, everything works for `clEnqueueSVMMemFill()` -  `clGetEventProfilingInfo()` returns correct info, copying more data takes more time.

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-10-27 20:49:02 UTC  
> Url: https://github.com/boostorg/compute/issues/528#issuecomment-151639274  

> Yes, it seems that clEnqueueSVMMemcpy API is not working as expected. I'll check it further and do the needful.  
> - dipak (https://community.amd.com/message/2680759#2680759)  
  
The bug is confirmed. https://github.com/boostorg/compute/pull/529 fixes `fill_svm_buffer` and `sum_svm_kernel` test cases by replacing `clEnqueueSVMMemcpy()` with map/umap operations (by fixing I mean that now they work correctly on AMD GPU). That's done without any harm to those test cases.  
  
> In any case, it would probably be good to add this to test/quirks.hpp and still keep a test or two for enqueue_svm_memcpy() around for devices which support it correctly.  
  
With https://github.com/boostorg/compute/pull/529 only `copy_svm_ptr` test case does not work on AMD. It can not be fixed without changing the algorithm. I think in this case adding proper function to `test/quirks.hpp` - `bool bug_in_svmmemcpy(const boost::compute::device &device)` and using it in `copy_svm_ptr` test case is a good idea. What do you think?  
  
Let's keep this issue open until AMD fix the bug.
