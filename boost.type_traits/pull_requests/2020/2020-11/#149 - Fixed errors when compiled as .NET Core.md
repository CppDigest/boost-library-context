# #149 Fixed errors when compiled as .NET Core [Open]

> Username: mw4853  
> Created at: 2020-11-24 20:23:02 UTC  
> Updated at: 2020-11-24 20:23:02 UTC  
> Url: https://github.com/boostorg/type_traits/pull/149  

Fixed errors and warnings when C++/CLI projects are compiled as .NET  
Core (as opposed to .NET Framework). Visual C++ defines __CLR_VER and  
_MANAGED when compiled as .NET Framework, but Visual C++ only defines  
_MANAGED when compiled as .NET Core.  
Issue: https://github.com/boostorg/type_traits/issues/148

---
