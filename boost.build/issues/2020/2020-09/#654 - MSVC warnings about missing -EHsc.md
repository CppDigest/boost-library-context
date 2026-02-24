# #654 - MSVC warnings about missing /EHsc [Closed]

> Username: Lastique  
> Created at: 2020-09-06 19:20:21 UTC  
> Updated at: 2020-09-13 14:17:19 UTC  
> Closed at: 2020-09-13 14:17:19 UTC  
> Url: https://github.com/boostorg/build/issues/654  

Building b2 with MSVC-14.0 results in lots of warnings like following:  
  
```  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\exception(359): warning C4577: 'noexcept' used with no exception handling mode specified; termination on exception is not guaranteed. Specify /EHsc (compiling source file modules.cpp)  
```  
  
Add `/EHsc` flag to compiler options to resolve this.
