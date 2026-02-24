# #690 - b2 applies both cflags and cxxflags when compiling a C++ source [Closed]

> Username: pdimov  
> Created at: 2020-12-24 22:58:56 UTC  
> Updated at: 2020-12-26 02:40:14 UTC  
> Closed at: 2020-12-25 22:14:46 UTC  
> Url: https://github.com/boostorg/build/issues/690  

With the msvc toolset, `b2 cflags=/std:c17 cxxflags=/std:c++17` applies both flags to C++ source files, which causes an error. According to the documentation of the msvc toolset, only cxxflags and compileflags should apply to C++ sources.  
  
References: https://github.com/boostorg/exception/issues/34 https://github.com/microsoft/vcpkg/issues/15232

---

## Comment 1

> Username: playgithub  
> Created at: 2020-12-26 02:20:58 UTC  
> Url: https://github.com/boostorg/build/issues/690#issuecomment-751310325  

@grafikrobot  
Fixed?

---

## Comment 2

> Username: grafikrobot  
> Created at: 2020-12-26 02:40:14 UTC  
> Url: https://github.com/boostorg/build/issues/690#issuecomment-751311223  

Yes, fixed in develop.
