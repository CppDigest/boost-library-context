# #565 Update tests compile options in Jamfile.v2 [Closed]

> Username: jszuppe  
> Created at: 2016-03-07 11:16:17 UTC  
> Updated at: 2016-03-07 11:16:27 UTC  
> Closed at: 2016-03-07 11:16:27 UTC  
> Url: https://github.com/boostorg/compute/pull/565  

This is a fast fix for https://github.com/boostorg/compute/issues/563 (+ I've suppressed some other warning for MSVC that are just annoying) .   
  
I'll later remove lines  
  
```  
<toolset>gcc:<cxxflags>-Wno-deprecated-declarations # Deprecated declarations in CL/cl.h  
<toolset>clang:<cxxflags>-Wno-deprecated-declarations # Deprecated declarations in CL/cl.h  
<toolset>darwin:<cxxflags>-Wno-deprecated-declarations # Deprecated declarations in CL/cl.h  
<toolset>msvc:<cxxflags>/wd4996 # Deprecated declarations in CL/cl.h  
```  
  
and suppress deprecated declarations warning (related to CL/cl.h) in the code.

---
