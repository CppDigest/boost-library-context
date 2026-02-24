# #12 have jump,make_fcontext show up as importable [Closed]

> Username: haubi  
> Created at: 2015-04-23 11:47:45 UTC  
> Updated at: 2015-04-28 14:46:47 UTC  
> Closed at: 2015-04-25 14:01:25 UTC  
> Url: https://github.com/boostorg/context/pull/12  

On AIX, building with aix-soname=svr4, nm is used to list public symbols  
in object files. This fixes the assembly to have make_fcontext and  
jump_fcontext show up as public symbols.  
  
The problem actually shows up when linking the coroutine library against the context library when built with aix-soname=svr4.   
However, except for this patch allowing to compile and link even coroutine, I'm unsure if the assembly is correct from all aspects, like alignment, TOC, and whether it works.

---

## Comment 1

> Username: olk  
> Created_at: 2015-04-25 14:01:23 UTC  
> Url: https://github.com/boostorg/context/pull/12#issuecomment-96209953  

thx, I've applied the patch to branch develop

---

## Comment 2

> Username: xantares  
> Created_at: 2015-04-28 14:40:05 UTC  
> Updated_at: 2015-04-28 14:46:47 UTC  
> Url: https://github.com/boostorg/context/pull/12#issuecomment-97087378  

hi @haubi, @olk,  
I'm experiencing a similar issue with boost 1.58 for mingw64 (it was fine for 1.57):  
  
```  
gcc.link.dll bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_coroutine-mt.dll.a  
bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/detail/coroutine_context.o:coroutine_context.cpp:(.text+0x4a): undefined reference to `_imp__make_fcontext'  
bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/detail/coroutine_context.o:coroutine_context.cpp:(.text+0xc7): undefined reference to `_imp__jump_fcontext'  
```  
  
I use this patch: http://pkgs.fedoraproject.org/cgit/mingw-boost.git/tree/boost-mingw.patch  
how can I fix it for mingw too ?

---
