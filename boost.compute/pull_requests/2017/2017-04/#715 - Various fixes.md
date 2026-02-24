# #715 Various fixes [Merged]

> Username: jszuppe  
> Created at: 2017-04-25 18:42:45 UTC  
> Updated at: 2017-04-27 18:58:02 UTC  
> Merged at: 2017-04-27 18:58:02 UTC  
> Closed at: 2017-04-27 18:58:02 UTC  
> Url: https://github.com/boostorg/compute/pull/715  

https://github.com/boostorg/compute/commit/2a6995581432ab602459aff81a1ecee6e175b732 is the most interesting bug-fix. `clEnqueueWriteBuffer` is not "100% blocking". OpenCL specification only promises that for `clEnqueueWriteBuffer`:  
  
>if blocking_write is `CL_TRUE`, the OpenCL implementation copies the data referred to by `ptr` and enqueues the write operation in the command-queue. The memory pointed to by `ptr` can be reused by the application after the `clEnqueueWriteBuffer` call returns.  
  
Which means that blocking `clEnqueueWriteBuffer` can return after it copies memory pointer by `ptr` to some temporary memory (on host), and it doesn't have to wait for the write to the buffer to be completed. (To be honest OpenCL specification is not 100% clear about that.) Anyway, in case of the Intel implementation for CPU, `clEnqueueWriteBuffer` does not wait for the data to be written into the buffer. Every other platform waits (AMD, NVIDIA, pocl), but the fact is that Intel implementation is in line with the specification. (See also https://software.intel.com/en-us/forums/opencl/topic/731519.)  
  
That caused random errors when writing and reading using `[]` operator, because each call of `[]` operator creates it's own temporary command queue (so calls were not ordered). Long story short: It was possible to do `vector[0] = 3;` and right after that `vector[0] == 3` could be `false`.

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-04-25 19:54:30 UTC  
> Url: https://github.com/boostorg/compute/pull/715#issuecomment-297146006  

[![Coverage Status](https://coveralls.io/builds/11237898/badge)](https://coveralls.io/builds/11237898)  
  
Coverage decreased (-0.004%) to 83.695% when pulling **beb2476cb0e70207c5c6b98f9f38d92742a2e600 on haahh:pr_fixes** into **debbf80b4d98b3cabcee672a96e7ed43878b23dc on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2017-04-26 10:58:16 UTC  
> Url: https://github.com/boostorg/compute/pull/715#issuecomment-297356489  

[![Coverage Status](https://coveralls.io/builds/11247541/badge)](https://coveralls.io/builds/11247541)  
  
Coverage decreased (-0.02%) to 83.676% when pulling **0f5c49d5f5f12d9a8b32e2193465f2279fb21b16 on haahh:pr_fixes** into **debbf80b4d98b3cabcee672a96e7ed43878b23dc on boostorg:develop**.

---
