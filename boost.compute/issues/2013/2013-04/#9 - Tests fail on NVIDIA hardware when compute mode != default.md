# #9 - Tests fail on NVIDIA hardware when compute mode != default [Closed]

> Username: ddemidov  
> Created at: 2013-04-18 09:56:37 UTC  
> Updated at: 2013-04-27 13:58:23 UTC  
> Closed at: 2013-04-27 13:58:23 UTC  
> Url: https://github.com/boostorg/compute/issues/9  

Hello Kyle,  
  
I am not sure about severity of this issue: some tests fail on NVIDIA GPUs when the GPUs are not in DEFAULT compute mode (that is, either EXCLUSIVE_THREAD or EXCLUSIVE_PROCESS).  
  
```  
$ sudo nvidia-smi -c 3  
Set compute mode to EXCLUSIVE_PROCESS for GPU 0000:04:00.0.  
Set compute mode to EXCLUSIVE_PROCESS for GPU 0000:05:00.0.  
Set compute mode to EXCLUSIVE_PROCESS for GPU 0000:08:00.0.  
All done.  
  
$ BOOST_COMPUTE_DEFAULT_DEVICE="Tesla C2075" make test  
...  
The following tests FAILED:  
      8 - core.kernel (Failed)  
     13 - algorithm.accumulate (Failed)  
     18 - algorithm.copy (Failed)  
     20 - algorithm.count (Failed)  
     31 - algorithm.inplace_reduce (Failed)  
     38 - algorithm.partition (Failed)  
     41 - algorithm.reduce (Failed)  
     45 - algorithm.scan (Failed)  
     47 - algorithm.sort (Failed)  
     49 - algorithm.transform (Failed)  
     52 - container.array (Failed)  
     54 - container.flat_set (Failed)  
     66 - blas.gemm (Failed)  
     70 - ext.complex (Failed)  
     74 - ext.tuple (Failed)  
...  
$ ./test_kernel   
Running 3 test cases...  
/home/demidov/work/opencl/compute/include/boost/compute/context.hpp(45): fatal error in "boost::compute::context::context(const boost::compute::device&, const cl_context_properties*)": std::exception: Invalid Device  
/home/demidov/work/opencl/compute/test/test_kernel.cpp(49): last checkpoint  
  
*** 1 failure detected in test suite "TestKernel"  
```  
  
All of these tests have several cases inside them, and each case initializes its own OpenCL context. But some tests with multiple cases do pass (e.g. core.buffer). Is it possible that some resources are not freed in failing test cases, and so OpenCL context is not destroyed there?  
  
The issue could in principle be solved by introducing global fixture that would hold an OpenCL context.  
  
This, of course, would not be a problem in a usual application, since an OpenCL context would only be created once there. But it looks like there is a potential for memory leaks.

---

## Comment 1

> Username: ddemidov  
> Created at: 2013-04-19 11:02:19 UTC  
> Url: https://github.com/boostorg/compute/issues/9#issuecomment-16647314  

a8b2c31 accidentally contained some temporary testing code. 5d77bbe corrects that.

---

## Comment 2

> Username: ddemidov  
> Created at: 2013-04-19 11:05:29 UTC  
> Url: https://github.com/boostorg/compute/issues/9#issuecomment-16647427  

It is clear now that it is impossible to completely clean up OpenCL resources before program exit: some of those are held by static variables. Same problem exists in vexcl.

---

## Comment 3

> Username: kylelutz  
> Created at: 2013-04-24 00:24:47 UTC  
> Url: https://github.com/boostorg/compute/issues/9#issuecomment-16899216  

Interesting. I'll try to reproduce this and look into it more. Ideally it should work.

---

## Comment 4

> Username: kylelutz  
> Created at: 2013-04-27 13:58:23 UTC  
> Url: https://github.com/boostorg/compute/issues/9#issuecomment-17116534  

Merged: dde1747b328065cc4157645f8b6ca8fc776d082f  
  
Thanks!
