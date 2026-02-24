# #529 Fix OpenCL 2.0 test cases in test_fill and test_svm_ptr [Merged]

> Username: jszuppe  
> Created at: 2015-10-24 19:33:19 UTC  
> Updated at: 2016-05-01 11:19:38 UTC  
> Merged at: 2015-10-30 04:31:46 UTC  
> Closed at: 2015-10-30 04:31:46 UTC  
> Url: https://github.com/boostorg/compute/pull/529  

See #528.  
  
I replaced broken `enqueue_svm_memcpy()` operations with map, copying and unmap in `test_fill.cpp` and `test_svm_ptr.cpp`.  
  
I also added required `-cl-std=CL2.0` build option in `sum_svm_kernel` test case ([clBuildProgram](https://www.khronos.org/registry/cl/sdk/2.0/docs/man/xhtml/clBuildProgram.html): _Applications are required to specify the –cl-std=CL2.0 option if they want to compile or build their programs with OpenCL C 2.0._").  
  
This branch is based on develop, because otherwise there are conflicts.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-10-29 11:59:55 UTC  
> Url: https://github.com/boostorg/compute/pull/529#issuecomment-152157525  

I've pushed two more commits  
  
Now test case copy_svm_ptr is skipped on AMD devices (see #528 if you want to know why). I also added more tests for enqueue_svm_memcpy() for devices which support it correctly.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-10-30 04:31:43 UTC  
> Url: https://github.com/boostorg/compute/pull/529#issuecomment-152412415  

Looks good, thanks for adding it to `quirks.hpp`.

---
