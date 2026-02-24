# #2809 - Double definition of zlib DEF_MEM_LEVEL [Closed]

> Username: sunew84  
> Created at: 2024-01-31 09:58:36 UTC  
> Updated at: 2024-01-31 14:29:07 UTC  
> Closed at: 2024-01-31 14:23:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2809  

For a cpp project I am crosscompiling in my toolchain zlib (1.3.1), boost (1.83.0) and boost.beast (347).  
The crosscompilation for arm-linux-gnueabihf completes successfully, but in compilation I get this error:  
  
```  
In file included from ..:  
/usr/arm-linux-gnueabihf/include/zip.h:81:27: error: expected unqualified-id before numeric constant  
   81 | # define DEF_MEM_LEVEL 8  
      | ^  
/usr/arm-linux-gnueabihf/include/boost/beast/zlib/detail/deflate_stream.hpp:113:35: note: in expansion of macro 'DEF_MEM_LEVEL'  
  113 | static std::uint8_t constexpr DEF_MEM_LEVEL = max_mem_level;  
      | ^~~~~~~~~~~~~  
```  
  
In practice there is a double definition of the variable DEF_MEM_LEVEL.  
1) Is there a possibility to exclude in Beast the use of the internal zlib in the beast/zlib folder (in crosscompilation and/or in preprocessor)? I have searched your documentation but have not found anything about it...  
2) If step 1) is not feasible, can you rename the static variable std::uint8_t constexpr DEF_MEM_LEVEL within your code to resolve the conflict in the next version?  
  
Thank you for your help!

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-31 10:49:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2809#issuecomment-1918858415  

It appears that there is a conflict with macro names in https://github.com/madler/zlib/blob/develop/contrib/minizip/zip.h.  
We should use lowercase names instead.  
  
> If step 1) is not feasible, can you rename the static variable std::uint8_t constexpr DEF_MEM_LEVEL within your code to resolve the conflict in the next version?  
  
Yes, I believe this will be fixed for the next release.

---

## Comment 2

> Username: sunew84  
> Created at: 2024-01-31 14:29:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2809#issuecomment-1919216764  

Thanks!
