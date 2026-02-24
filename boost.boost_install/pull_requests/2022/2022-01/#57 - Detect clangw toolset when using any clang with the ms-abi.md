# #57 Detect clangw toolset when using any clang with the ms-abi [Merged]

> Username: cdglove  
> Created at: 2022-01-17 22:22:13 UTC  
> Updated at: 2022-01-18 04:49:54 UTC  
> Merged at: 2022-01-18 04:49:54 UTC  
> Closed at: 2022-01-18 04:49:54 UTC  
> Url: https://github.com/boostorg/boost_install/pull/57  

When compiling with clang, cmake only sets MSVC when using clang-cl as  
it's meant as an indication of cl.exe command line compatibility.  
  
In this case, if one is using clang++ with the ms-abi, cmake  
incorrectly takes the else branch here and library detection fails.  
  
If instead we use MSVC_VERSION, which is empty unless a compiler  
with _MSC_VER set is being used, then we get the correct behaviour.  
  
https://cmake.org/cmake/help/latest/variable/MSVC.html  
  
https://cmake.org/cmake/help/latest/variable/MSVC_VERSION.html

---
