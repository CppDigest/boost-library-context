# #244 - Compilation  with Intel oneAPI: Many warnings [Open]

> Username: MarDiehl  
> Created at: 2025-02-22 10:42:16 UTC  
> Updated at: 2025-02-22 12:31:35 UTC  
> Url: https://github.com/boostorg/log/issues/244  

When installing boost with Intel oneAPI using the recommended way (bootstrap), the compiler gives the following warning  
  
```  
These options as listed are not supported.  
For more information, use '-qnextgen-diag'.  
option list:   
	-ip  
	-wd177,780,2196,1782,193,304,981,1418,411,734,279  
```  
  
This is defined here:  
https://github.com/boostorg/log/blob/6366d73335d298c8c5c7f7bd80f6931fedb0df1b/build/Jamfile.v2#L98  
  
For CMake, this is not an issue because the new, LLVM-based compiler identifies as `IntelLLVM`, not `Intel` (https://cmake.org/cmake/help/latest/variable/CMAKE_LANG_COMPILER_ID.html) and, hence, the options are not added here:  
https://github.com/boostorg/log/blob/6366d73335d298c8c5c7f7bd80f6931fedb0df1b/CMakeLists.txt#L416

---

## Comment 1

> Username: Lastique  
> Created at: 2025-02-22 11:39:13 UTC  
> Url: https://github.com/boostorg/log/issues/244#issuecomment-2676159780  

As far as I can see, Boost.Build does not discriminate between the legacy Intel compiler and the newer LLVM-based one - both are identified as `intel-linux` (on Linux platform). So there is no way I could add these flags for the legacy compiler but not the newer one.  
  
I can see you've already created an [issue](https://github.com/bfgroup/b2/issues/413) for `b2` to support the new compiler. Until that issue is resolved, there's nothing I can do in Boost.Log.  
  
In the meantime, you can patch your local Boost by removing the compiler options that don't work.

---

## Comment 2

> Username: MarDiehl  
> Created at: 2025-02-22 12:31:34 UTC  
> Url: https://github.com/boostorg/log/issues/244#issuecomment-2676179089  

yes, unfortunately the PR at `b2` is stuck because there is no test setup for Intel compilers.  
  
As far as I see, the flags specific to `boost.log` only disable warnings. So my suggestion would be to simply remove them to reverse the behavior: Instead of showing warnings for new compiler versions, show (other) warnings for old compiler versions. Unfortunately, one would still get the warning about `-ip` which is caused by `b2`.
