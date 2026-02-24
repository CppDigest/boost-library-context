# #118 - fatal error C1083: Cannot open include file: 'cxxabi.h': No such file or directory [Closed]

> Username: MathiasCombalbert  
> Created at: 2022-05-16 14:16:00 UTC  
> Updated at: 2022-05-17 13:33:39 UTC  
> Closed at: 2022-05-17 13:33:39 UTC  
> Url: https://github.com/boostorg/core/issues/118  

Hello,  
  
I'm trying to port code from linux to visual studio 2019, cmake correctly finds boost, but when building I get this error :   
C:\local\boost_1_67_0\boost/core/demangle.hpp(32): fatal error C1083: Cannot open include file: 'cxxabi.h': No such file or directory  
I tried with boost 1_67 and boost 1_74. It was working fine last time the code has been ported to visual studio 2012.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-16 15:09:07 UTC  
> Url: https://github.com/boostorg/core/issues/118#issuecomment-1127795062  

You must have some issue with system macros, because given the code that detects `cxxabi.h` presence:  
  
https://github.com/boostorg/core/blob/c0e2211c2b12a618c414b5114a25351f81a9edf8/include/boost/core/demangle.hpp#L23-L29  
  
you have either `__GLIBCXX__` or `__GLIBCPP__` defined. Those macros are defined by libstdc++, which is not used with Visual Studio.  
  
Alternatively, you are defining `BOOST_CORE_HAS_CXXABI_H` somewhere. Which you shouldn't be doing.  
  
In any case, you'll have to investigate this on you end because our tests on MSVC pass. Check what preprocessor macros are defined in your case, check what headers are included and where they are located (maybe you're pulling some unintended headers from libstdc++?). Try to examine preprocessed output of a small code sample that reproduces the error (for MSVC you can use [`/P`](https://docs.microsoft.com/en-us/cpp/build/reference/p-preprocess-to-a-file?view=msvc-170) argument).

---

## Comment 2

> Username: MathiasCombalbert  
> Created at: 2022-05-17 13:33:39 UTC  
> Url: https://github.com/boostorg/core/issues/118#issuecomment-1128877160  

Thank you, I tried to build my project in a new windows vm and it seems to work just fine. Maybe I manually broke something on the last windows vm, no idea of what happened.
