# #30 Fix conversion warnings [Merged]

> Username: Flamefire  
> Created at: 2017-10-07 06:59:05 UTC  
> Updated at: 2019-12-14 09:31:49 UTC  
> Merged at: 2019-12-14 09:31:46 UTC  
> Closed at: 2019-12-14 09:31:46 UTC  
> Url: https://github.com/boostorg/nowide/pull/30  

Compiling with VS gives (correctly):  
  
>  ...nowide\src\iostream.cpp(53): warning C4244: 'initializing': conversion from '__int64' to 'int', possible loss of data [...\nowide\src\nowide-static.vcxproj]  
>          ...nowide\src\iostream.cpp(86): warning C4244: 'argument': conversion from '__int64' to 'DWORD', possible loss of data [...\nowide\src\nowide-static.vcxproj]  
>          ...nowide\src\iostream.cpp(88): warning C4267: 'return': conversion from 'size_t' to 'int', possible loss of data [...\nowide\src\nowide-static.vcxproj]  
>          ...nowide\src\iostream.cpp(161): warning C4267: 'argument': conversion from 'size_t' to 'DWORD', possible loss of data [...\nowide\src\nowide-static.vcxproj]  
>          ...nowide\src\iostream.cpp(169): warning C4244: '=': conversion from '__int64' to 'int', possible loss of data [...\nowide\src\nowide-static.vcxproj]  
>          ...nowide\src\iostream.cpp(172): warning C4244: '=': conversion from '__int64' to 'int', possible loss of data [...\nowide\src\nowide-static.vcxproj]

---
