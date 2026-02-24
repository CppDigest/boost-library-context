# #217 Use dllimport/dllexport with clang and gcc on Cygwin. [Merged]

> Username: Lastique  
> Created at: 2018-03-11 14:42:54 UTC  
> Updated at: 2018-03-16 19:21:20 UTC  
> Merged at: 2018-03-16 18:15:30 UTC  
> Closed at: 2018-03-16 18:15:30 UTC  
> Url: https://github.com/boostorg/config/pull/217  

This is required at least for Boost.WinAPI to match the declarations in  
Windows SDK headers, which use dllimport.  
  
Also, for clang on Windows, use dllimport/dllexport even beyond Cygwin.  
The compiler does support these attributes, and it should for  
compatibility with Windows headers.

---
