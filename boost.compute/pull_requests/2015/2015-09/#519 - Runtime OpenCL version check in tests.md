# #519 Runtime OpenCL version check in tests [Merged]

> Username: jszuppe  
> Created at: 2015-09-24 13:58:10 UTC  
> Updated at: 2016-05-01 11:19:32 UTC  
> Merged at: 2015-09-29 02:52:10 UTC  
> Closed at: 2015-09-29 02:52:10 UTC  
> Url: https://github.com/boostorg/compute/pull/519  

I installed AMD APP SKD 3.0 which has OpenCL 2.0 `cl.h` (`CL_VERSION_2_0` macro is defined in it) and since my GPU does not support OpenCL 2.0 I got errors with some tests.   
  
I fixed it by adding runtime OpenCL version check in those tests. I did the same with tests that require OpenCL 1.1 or 1.2. Now you can have `cl.h` defining `CL_VERSION_2_0` and OpenCL 1.0 device and tests should pass.

---
