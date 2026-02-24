# #99 - warnings/errors compiling with /clr option in msvc [Closed]

> Username: dougswallow  
> Created at: 2018-12-29 20:05:44 UTC  
> Updated at: 2021-02-10 13:40:02 UTC  
> Closed at: 2019-01-06 08:42:07 UTC  
> Url: https://github.com/boostorg/type_traits/issues/99  

As someone else reported in the mailing list:  
  
> 1>c:\quinesoftwarebase\include\boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp(114):  
> error C2764: 'C': template parameter not used or deducible in partial  
> specialization 'boost::is_member_function_pointer<Ret(Args...)>'  
> 1>c:\quinesoftwarebase\include\boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp(125):  
> warning C4575: '__vectorcall' incompatible with the '/clr' option:  
> converting to '__stdcall'   
  
Basically the __clrcall variant isn't using the C template parameter, so I adjusted them to match the other calling convention variants.  
  
Several calling conventions exist/do not exist/default differently based on x86/x64 settings and enabling CLR so adjusted the #if logic accordingly.  
  
I've adjusted these files to deal with the problems; provided to assist:  
  
https://1drv.ms/f/s!Auh9GRj1YtuPuP545hS6BPhF0apLzQ

---

## Comment 1

> Username: zvrba  
> Created at: 2018-12-30 06:16:17 UTC  
> Updated at: 2018-12-30 06:21:25 UTC  
> Url: https://github.com/boostorg/type_traits/issues/99#issuecomment-450541674  

Additional information: I use Visual Studio 15.7.2, toolset v14.1, compiler version string Microsoft(R) C/C++ Optimizing Compiler Version 19.14.26429.4 for x64 and I target C++/CLI for x64. The project is an ordinary C++ project being built as x64 DLL/EXE, with only a couple of files being compiled for C++/CLI (i.e., I have turned on `/clr` on individual files, not on the whole project).  
  
As I wrote in the mail, stdcall, fastcall and cdecl conventions are also disabled when compiling for x64, regardless of CLI compilation.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-12-30 08:46:16 UTC  
> Url: https://github.com/boostorg/type_traits/issues/99#issuecomment-450546989  

From Microsoft's docs:  
  
"On ARM and x64 processors, __stdcall is accepted and ignored by the compiler; on ARM and x64 architectures, by convention, arguments are passed in registers when possible, and subsequent arguments are passed on the stack."  
  
Effectively all the calling conventions get mapped to the same thing so you need to disable specialisations on them to prevent duplications.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-01-06 08:42:07 UTC  
> Url: https://github.com/boostorg/type_traits/issues/99#issuecomment-451725224  

Fixed in develop.

---

## Comment 4

> Username: erikeldh  
> Created at: 2021-02-10 13:40:02 UTC  
> Url: https://github.com/boostorg/type_traits/issues/99#issuecomment-776712163  

Which version was this fixed in? I'm hitting this in 1.75   
  
```  
boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp(121,4): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp(121,113): error C2953: 'boost::is_member_function_pointer<Ret(__stdcall C::* )(Args...)>': class template has already been defined  
```
