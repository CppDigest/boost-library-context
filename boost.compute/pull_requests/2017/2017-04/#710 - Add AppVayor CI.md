# #710 Add AppVayor CI [Merged]

> Username: jszuppe  
> Created at: 2017-04-21 18:52:30 UTC  
> Updated at: 2017-05-04 05:06:07 UTC  
> Merged at: 2017-05-04 05:06:01 UTC  
> Closed at: 2017-05-04 05:06:01 UTC  
> Url: https://github.com/boostorg/compute/pull/710  

This PR adds AppVayor CI and fixes some bugs in tests.  
  
https://github.com/boostorg/compute/commit/2a6995581432ab602459aff81a1ecee6e175b732 is the most interesting bug-fix. `clEnqueueWriteBuffer` is not "100% blocking". OpenCL specification only promises that for `clEnqueueWriteBuffer`:  
  
>if blocking_write is `CL_TRUE`, the OpenCL implementation copies the data referred to by `ptr` and enqueues the write operation in the command-queue. The memory pointed to by `ptr` can be reused by the application after the `clEnqueueWriteBuffer` call returns.  
  
Which means that blocking `clEnqueueWriteBuffer` can return after it copies memory pointer by `ptr` to some temporary memory (on host), and it doesn't have to wait for the write to the buffer to be completed. (To be honest OpenCL specification is not 100% clear about that.) Anyway, in case of the Intel implementation for CPU, `clEnqueueWriteBuffer` does not wait for the data to be written into the buffer. Every other platform waits (AMD, NVIDIA, pocl), but the fact is that Intel implementation is in line with the specification. (See also https://software.intel.com/en-us/forums/opencl/topic/731519.)  
  
That caused random errors when writing and reading using `[]` operator, because each call of `[]` operator creates it's own temporary command queue (so calls were not ordered). Long story short: It was possible to do `vector[0] = 3;` and right after that `vector[0] == 3` could be `false`.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2017-04-21 19:03:30 UTC  
> Updated_at: 2017-05-03 17:04:48 UTC  
> Url: https://github.com/boostorg/compute/pull/710#issuecomment-296279312  

@kylelutz I guess we need some connection with AppVeyor CI in repository settings. I don't have permissions to do it. When it's activate, send me the code with the badge, so I'll update commit https://github.com/boostorg/compute/pull/710/commits/1e8d292478ca639e32aff6105d793a7e5b3f3037. It would be great if I can have access to the AppVeyor CI of Boost.Compute like I have in Travis CI.  
  
Check https://ci.appveyor.com/project/jszuppe/compute-e97yn/build/1.0.17 to see how AppVeyor builds  work.

---

## Comment 2

> Username: coveralls  
> Created_at: 2017-04-21 20:16:38 UTC  
> Url: https://github.com/boostorg/compute/pull/710#issuecomment-296295618  

[![Coverage Status](https://coveralls.io/builds/11187961/badge)](https://coveralls.io/builds/11187961)  
  
Coverage decreased (-0.004%) to 83.357% when pulling **1e8d292478ca639e32aff6105d793a7e5b3f3037 on haahh:pr_add_appvayor_ci** into **2e790df587f42a11b4384ba1b80287727d527d71 on boostorg:develop**.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2017-04-25 16:35:20 UTC  
> Url: https://github.com/boostorg/compute/pull/710#issuecomment-297088739  

I'll move fixes to separate pull request.

---

## Comment 4

> Username: coveralls  
> Created_at: 2017-04-29 12:59:06 UTC  
> Url: https://github.com/boostorg/compute/pull/710#issuecomment-298167595  

[![Coverage Status](https://coveralls.io/builds/11302687/badge)](https://coveralls.io/builds/11302687)  
  
Coverage remained the same at 83.676% when pulling **531c475b6dbe13c275f4f256974e652574d4f3ca on haahh:pr_add_appvayor_ci** into **863371d2905c7369ba3ba9554d12308d20dbf1eb on boostorg:develop**.

---

## Comment 5

> Username: coveralls  
> Created_at: 2017-05-03 19:29:40 UTC  
> Url: https://github.com/boostorg/compute/pull/710#issuecomment-299011411  

[![Coverage Status](https://coveralls.io/builds/11357043/badge)](https://coveralls.io/builds/11357043)  
  
Coverage remained the same at 83.676% when pulling **f3a2e4c605285a3735055bc361776529ed709686 on jszuppe:pr_add_appvayor_ci** into **863371d2905c7369ba3ba9554d12308d20dbf1eb on boostorg:develop**.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2017-05-04 05:06:06 UTC  
> Url: https://github.com/boostorg/compute/pull/710#issuecomment-299097767  

Awesome! Merged!

---
