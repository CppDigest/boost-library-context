# #463 - Compile error in test cases when using OpenCL 1.1 [Closed]

> Username: youngtaekoh  
> Created at: 2015-06-05 04:59:36 UTC  
> Updated at: 2017-04-09 18:50:32 UTC  
> Closed at: 2017-04-09 18:50:32 UTC  
> Url: https://github.com/boostorg/compute/issues/463  

When compiling boost::compute in windows with MSVC 2010 & NVIDIA GTX 580, `test/test_buffer.cpp` and `test/test_command_queue.cpp` causes compile error. This seems to be related to OpenCL 1.1 header file. (Since I didn’t see any problem when compiling this in OpenCL 1.2 environment)  
  
Solution was removing `BOOST_COMPUTE_CL_CALLBACK` in `test/test_buffer.cpp:129` and adding `BOOST_COMPUTE_CL_CALLBACK` to `test/test_command_queue.cpp:203`  
  
Also, `test_buffer` fails with NVIDIA GTX 580, but adding `queue.finish()` to line 142 seems to solve it. It seems like that this is also a bug in NVIDIA OpenCL because it was fine when I executed it with CPU. There is also a bug in `test_vector.cpp` but I cannot find workarounds.  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-06-13 03:51:23 UTC  
> Url: https://github.com/boostorg/compute/issues/463#issuecomment-111667231  

Can you post the compiler errors you're getting? For the other changes, could you submit a pull-request with the patches so I can test them out? Thanks!

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-04-09 18:43:36 UTC  
> Updated at: 2017-04-09 18:50:31 UTC  
> Url: https://github.com/boostorg/compute/issues/463#issuecomment-292804140  

* I think bug in `test_buffer.cpp` was fixed by https://github.com/boostorg/compute/pull/693/files#diff-3b128b5d561d95e3e1bf88e7e55944bb.   
* [clEnqueueNativeKernel](https://www.khronos.org/registry/OpenCL/sdk/1.1/docs/man/xhtml/clEnqueueNativeKernel.html) does not require `user_func` function to defined with  `CL_CALLBACK`.  
  
I don't see those bugs on GTX 1080/Linux/latest drivers. Reopen if still getting those errors.
