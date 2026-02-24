# #148 - Warning C4575 when compiled as a C++/CLI .Net Core project with Visual Studio 2019 v16.8 [Open]

> Username: mw4853  
> Created at: 2020-11-16 18:53:47 UTC  
> Updated at: 2021-02-10 14:35:38 UTC  
> Url: https://github.com/boostorg/type_traits/issues/148  

If you include a boost header (such as boost/algorithm/string.hpp) that ultimately includes is_funcion_cxx_11.hpp in a C++/CLI .NET Core (NOT the normal .NET Framework) project, you will see a series of compiler warnings similar to the following:  
  
> boost-1.74\boost\type_traits\detail\is_function_cxx_11.hpp(114,4): warning C4575: '__vectorcall' incompatible with the '/clr' option: converting to '__stdcall'  
  
https://github.com/boostorg/type_traits/blob/c6c7ff164789e7ca1d71745714cb27fae956360b/include/boost/type_traits/detail/is_function_cxx_11.hpp#L114  
  
There is no warning here if the C++/CLI project is compiled as .NET Framework instead. It looks like __CLR_VER is defined when building as .NET Framework, but not when building as .NET Core?  
  
How to convert your C++/CLI project to build as .NET Core (instead of the normal .NET Framework):  
https://docs.microsoft.com/en-us/dotnet/core/porting/cpp-cli

---

## Comment 1

> Username: mw4853  
> Created at: 2020-11-16 20:17:58 UTC  
> Url: https://github.com/boostorg/type_traits/issues/148#issuecomment-728300683  

Maybe __CLR_VER should be replaced with _MANAGED ?  
https://docs.microsoft.com/en-us/cpp/preprocessor/predefined-macros?view=msvc-160

---

## Comment 2

> Username: millerrt3  
> Created at: 2020-11-30 15:45:20 UTC  
> Url: https://github.com/boostorg/type_traits/issues/148#issuecomment-735867105  

@mw4853 I believe that this is effectively a duplicate of #123 (even though the specific warning is different in your case, the underlying cause is the same). I filed PR #130 to fix #123 a while back, but there doesn't seem to be much movement towards merging it (or any other fix for the issue).

---

## Comment 3

> Username: mw4853  
> Created at: 2020-11-30 15:53:41 UTC  
> Url: https://github.com/boostorg/type_traits/issues/148#issuecomment-735872229  

@bionicOnion - Darn. I was really hoping to avoid modifying my local copy of Boost, but, it looks like that is what I am going to have to do.

---

## Comment 4

> Username: erikeldh  
> Created at: 2021-02-10 14:35:38 UTC  
> Url: https://github.com/boostorg/type_traits/issues/148#issuecomment-776749876  

`boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp(121,4): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp(121,113): error C2953: 'boost::is_member_function_pointer<Ret(__stdcall C::* )(Args...)>': class template has already been defined`  
  
I believe this still to be an issue with boost 1.75
