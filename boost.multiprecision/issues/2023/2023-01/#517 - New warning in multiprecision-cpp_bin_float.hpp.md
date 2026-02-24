# #517 - New warning in multiprecision/cpp_bin_float.hpp [Closed]

> Username: greenkalx  
> Created at: 2023-01-12 03:16:16 UTC  
> Updated at: 2023-01-12 19:54:54 UTC  
> Closed at: 2023-01-12 19:54:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/517  

A new warning appears in cpp_bin_float.hpp due to warning suppression being popped twice.  
We treat warnings as errors for some projects.  
See attached patch that we'll use to workaround this.  
  
This is a new warning in 1_81, it wasn't an issue in 1_80.  
System is Windows 10 with long paths enabled, compiling with Visual Studio 2022 C++17.  
  
[0011-vs2022-build-fixups.patch](https://github.com/boostorg/multiprecision/files/10397866/0011-vs2022-build-fixups.patch)  
  
The build error:  
```  
1>Note: including file:         c:\work\external\libs\Boost\boost_1_81_0\boost/multiprecision/cpp_bin_float.hpp  
1>c:\work\external\libs\Boost\boost_1_81_0\boost\multiprecision\cpp_bin_float.hpp(2150,17): error C2220: the following warning is treated as an error  
1>c:\work\external\libs\Boost\boost_1_81_0\boost\multiprecision\cpp_bin_float.hpp(2150,17): warning C4193: #pragma warning(pop): no matching '#pragma warning(push)'  
```

---

## Comment 1

> Username: mborland  
> Created at: 2023-01-12 16:56:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/517#issuecomment-1380718966  

This was fixed by https://github.com/boostorg/multiprecision/commit/3f42566a7d5ab4d7d9ed81613fa61609e2084e07. Are you using all of Boost, or the standalone package from our page? If you are using the latter we can cut a bug fix release.

---

## Comment 2

> Username: danwalshnitro  
> Created at: 2023-01-12 17:25:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/517#issuecomment-1380757165  

I'm not the original poster but we're seeing this issue when using Boost.Geometry through the boost conan package.

---

## Comment 3

> Username: mborland  
> Created at: 2023-01-12 17:31:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/517#issuecomment-1380763737  

> I'm not the original poster but we're seeing this issue when using Boost.Geometry through the boost conan package.  
  
Unfortunately for any packages besides the standalone on our page the fix will not be in until Boost 1.82 is released in the April timeframe.

---

## Comment 4

> Username: greenkalx  
> Created at: 2023-01-12 17:56:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/517#issuecomment-1380789407  

We are using all of Boost. We'll wait for the fix in a future release.
