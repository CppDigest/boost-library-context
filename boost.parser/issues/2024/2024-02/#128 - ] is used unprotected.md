# #128 - [[no_unique_address]] is used unprotected [Closed]

> Username: anarthal  
> Created at: 2024-02-28 12:35:46 UTC  
> Updated at: 2024-02-29 00:05:13 UTC  
> Closed at: 2024-02-29 00:05:13 UTC  
> Url: https://github.com/boostorg/parser/issues/128  

`[[no_unique_address]]` is C++20 but seems to be used unprotected, which triggers warnings by C++17 compilers.  
  
See this build (edited link): https://drone.cpp.al/boostorg/mysql/545/11/3  
  
Compiler details:  
* toolset=clang-7  
* cxxstd=17  
* variant=debug,release  
* warnings=extra

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-29 00:05:13 UTC  
> Url: https://github.com/boostorg/parser/issues/128#issuecomment-1970138332  

That warning is objectively dumb.  The whole point of an attribute's being ignored by the compiler if the compiler doesn't recognize it is that you can put it in your code, and enjoy the benefits when using later C++ standards, without needing macro guards around your code.  I'm not going to add such guards here.
