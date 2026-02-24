# #330 tests: explicit extensions required in modern CMake [Merged]

> Username: henryiii  
> Created at: 2021-09-14 20:56:45 UTC  
> Updated at: 2021-09-26 10:31:00 UTC  
> Merged at: 2021-09-26 10:31:00 UTC  
> Closed at: 2021-09-26 10:31:00 UTC  
> Url: https://github.com/boostorg/histogram/pull/330  

This produces warnings (and would produce errors if the upper limit was changed to 3.21 from the current 3.16). The correct fix is to be explicit.  
  
Example warning:  
  
```  
CMake Warning (dev) at benchmark/CMakeLists.txt:27 (add_executable):  
  Policy CMP0115 is not set: Source file extensions must be explicit.  Run  
  "cmake --help-policy CMP0115" for policy details.  Use the cmake_policy  
  command to set the policy and suppress this warning.  
  
  File:  
  
    /Users/henryschreiner/git/software/histogram/benchmark/axis_size.cpp  
Call Stack (most recent call first):  
  benchmark/CMakeLists.txt:34 (add_benchmark)  
This warning is for project developers.  Use -Wno-dev to suppress it.  
```

---

## Comment 1

> Username: HDembinski  
> Created_at: 2021-09-26 10:30:56 UTC  
> Url: https://github.com/boostorg/histogram/pull/330#issuecomment-927279999  

Thanks for the patch!

---
