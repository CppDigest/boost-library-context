# #99 - Error in win32_api.hpp [Closed]

> Username: FlowzZz  
> Created at: 2019-10-16 14:52:18 UTC  
> Updated at: 2020-06-15 21:48:20 UTC  
> Closed at: 2020-06-03 19:37:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/99  

Hello,  
I try to compile one of my project after upgrading to v16.3.4 of VS2019.  
Context :  
- Building with Release x64  
- Boost 1.71.0 (compiled with vcpkg)  
  
I get these errors :  
1>...\vcpkg_VS2019\installed\x64-windows\include\boost\interprocess\detail\win32_api.hpp(925,51): error C2116: 'CreateSemaphoreA': function parameter lists do not match between declarations  
1>...\vcpkg_VS2019\installed\x64-windows\include\boost\winapi\semaphore.hpp(25): message : see declaration of 'CreateSemaphoreA'  
1>...\vcpkg_VS2019\installed\x64-windows\include\boost\interprocess\detail\win32_api.hpp(925,51): error C2733: 'CreateSemaphoreA': you cannot overload a function with 'C' linkage  
1>...\vcpkg_VS2019\installed\x64-windows\include\boost\winapi\semaphore.hpp(25,1): message : see declaration of 'CreateSemaphoreA'  
1>...\vcpkg_VS2019\installed\x64-windows\include\boost\interprocess\detail\win32_api.hpp(932,49): error C2116: 'GetSystemInfo': function parameter lists do not match between declarations  
1>...\vcpkg_VS2019\installed\x64-windows\include\boost\winapi\system.hpp(31): message : see declaration of 'GetSystemInfo'  
1>D:\Commun\Codes\vcpkg_VS2019\installed\x64-windows\include\boost\interprocess\detail\win32_api.hpp(932,49): error C2733: 'GetSystemInfo': you cannot overload a function with 'C' linkage  
  
Is this a known problem ?

---

## Comment 1

> Username: FlowzZz  
> Created at: 2019-10-16 15:26:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/99#issuecomment-542757104  

I do not think this is not a good final solution, but I find an option in visual studio to skip these errors : add compile option : /Zc:externC-  
See : [https://developercommunity.visualstudio.com/content/problem/756694/including-windowsh-and-boostinterprocess-headers-l.html](https://developercommunity.visualstudio.com/content/problem/756694/including-windowsh-and-boostinterprocess-headers-l.html)

---

## Comment 2

> Username: ecatmur  
> Created at: 2019-10-23 17:48:50 UTC  
> Url: https://github.com/boostorg/interprocess/issues/99#issuecomment-545559983  

In the absence of a better solution, it would be a good idea to document the use of this compile option alongside https://github.com/boostorg/interprocess/blob/4a57769910cfbc2dc36fefa239c1482f77825ff3/doc/interprocess.qbk#L6671

---

## Comment 3

> Username: igaztanaga  
> Created at: 2020-06-03 19:39:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/99#issuecomment-638418837  

After:  
  
* Careful use of Boost.Winapi and use of SetFilePointer instead of SetFilePointerEx (which requires a _LARGE_INTEGER by value that difficults the use of windows.h in certain circustances)  
* Added tests to verify windows.h inclusion without BOOST_USE_WINDOWS_H does not break things  
  
the issue seems to be solved. Many thanks for the report!

---

## Comment 4

> Username: k15tfu  
> Created at: 2020-06-15 15:29:38 UTC  
> Url: https://github.com/boostorg/interprocess/issues/99#issuecomment-644205353  

@igaztanaga Which version of Windows SDK did you use when testing?

---

## Comment 5

> Username: igaztanaga  
> Created at: 2020-06-15 21:48:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/99#issuecomment-644408043  

I compiled the change with multiple Visual Studio versions from Visual 2010 to Visual 2019. In Visual 2019 I think the latest SDK version installed was 10.0.17763.0
