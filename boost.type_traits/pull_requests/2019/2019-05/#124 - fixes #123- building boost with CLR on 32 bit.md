# #124 fixes #123: building boost with CLR on 32 bit [Closed]

> Username: codingdave  
> Created at: 2019-05-21 13:08:43 UTC  
> Updated at: 2019-06-13 10:49:57 UTC  
> Closed at: 2019-06-13 10:49:57 UTC  
> Url: https://github.com/boostorg/type_traits/pull/124  

This change fixes the compilation issue on my side. I just blindly changed all definitions from   
> #ifndef _M_AMD64   
and replaced them with   
> #if !defined(_M_AMD64) && !defined(__CLR_VER)  
  
For me it does the trick (VS2017, CLR, C++17, 32 and 64 bit) but I don't know if it breaks other configurations.   
  
Kind regards  
Dave

---
