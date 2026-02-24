# #783 Fix Appveyor requesting missing OpenCL branch [Merged]

> Username: henryiii  
> Created at: 2018-05-18 15:39:43 UTC  
> Updated at: 2018-07-20 09:45:55 UTC  
> Merged at: 2018-07-20 09:45:55 UTC  
> Closed at: 2018-07-20 09:45:55 UTC  
> Url: https://github.com/boostorg/compute/pull/783  

Use `CL_TARGET_OPENCL_VERSION` to set a maximum version instead (not added to this PR unless AppVeyor fails). See https://github.com/KhronosGroup/OpenCL-Headers. The `master` branch is now the only branch.

---
