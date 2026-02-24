# #67 - C++/WinRT UWP App fails to compile when using C++20 [Closed]

> Username: JuliusZet  
> Created at: 2022-09-21 17:27:20 UTC  
> Updated at: 2023-04-29 02:42:33 UTC  
> Closed at: 2023-04-29 02:42:33 UTC  
> Url: https://github.com/boostorg/signals2/issues/67  

Hello  
  
I would like to use the Boost 1.80 Header-Only Libraries in my C++/WinRT UWP App. But when I switch the C++ Language Standard to "ISO C++20 Standard (/std:c++20)", then the app will not compile anymore.  
  
I get the following output:  
```  
Build started...  
1>------ Build started: Project: BlankApp1, Configuration: Debug x64 ------  
1>App.cpp  
1>MainPage.cpp  
1>C:\Users\juliuszet\source\repos\BlankApp1\BlankApp1\boost\signals2\detail\lwm_win32_cs.hpp(61,49): error C2371: 'InitializeCriticalSectionEx': redefinition; different basic types (compiling source file MainPage.cpp)  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\um\synchapi.h(155): message : see declaration of 'InitializeCriticalSectionEx' (compiling source file MainPage.cpp)  
1>C:\Users\juliuszet\source\repos\BlankApp1\BlankApp1\boost\signals2\detail\lwm_win32_cs.hpp(61,49): error C2733: 'InitializeCriticalSectionEx': you cannot overload a function with 'extern "C"' linkage (compiling source file MainPage.cpp)  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\um\synchapi.h(155,1): message : see declaration of 'InitializeCriticalSectionEx' (compiling source file MainPage.cpp)  
1>C:\Users\juliuszet\source\repos\BlankApp1\BlankApp1\boost\signals2\detail\lwm_win32_cs.hpp(61,49): error C2371: 'InitializeCriticalSectionEx': redefinition; different basic types (compiling source file App.cpp)  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\um\synchapi.h(155): message : see declaration of 'InitializeCriticalSectionEx' (compiling source file App.cpp)  
1>C:\Users\juliuszet\source\repos\BlankApp1\BlankApp1\boost\signals2\detail\lwm_win32_cs.hpp(61,49): error C2733: 'InitializeCriticalSectionEx': you cannot overload a function with 'extern "C"' linkage (compiling source file App.cpp)  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\um\synchapi.h(155,1): message : see declaration of 'InitializeCriticalSectionEx' (compiling source file App.cpp)  
1>C:\Program Files (x86)\Windows Kits\10\bin\10.0.19041.0\XamlCompiler\Microsoft.Windows.UI.Xaml.Common.targets(486,5): error MSB4181: The "CompileXaml" task returned false but did not log an error.  
1>Done building project "BlankApp1.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```  
  
Steps to reproduce:  
  
1. Create a new project in Visual Studio 2022. Project template: "Blank App (C++/WinRT)".  
![](https://i.imgur.com/MdW8Qgr.png)  
Do not change any settings during the project creation.  
2. Copy the "boost_1_80_0.7z\boost_1_80_0\boost" folder into your project directory (where your BlankApp1.vcxproj) is located.  
For me the path is "C:\Users\juliuszet\source\repos\BlankApp1\BlankApp1\" <-- boost folder goes here.  
3. Add `#include <boost/signals2.hpp>` to your MainPage.h file, right below the `#include "MainPage.g.h"`.  
4. Click Build --> Build Solution and notice, that it compiles successfully.  
5. Click Project --> Properties, Navigate to Configuration Properties --> C/C++ --> Language and change the C++ Language Standard to "ISO C++20 Standard (/std:c++20)".   
6. Click Build --> Build Solution and notice, that it does not compile anymore.  
  
After taking a closer look at the error messages in the output, I played around a bit and found out, that it does compile again, when changing a few lines of code in "boost\signals2\detail\lwm_win32_cs.hpp"  
  
replace this:  
```  
#if BOOST_PLAT_WINDOWS_RUNTIME  
extern "C" __declspec(dllimport) void __stdcall InitializeCriticalSectionEx(::_RTL_CRITICAL_SECTION *, unsigned long, unsigned long);  
#else  
extern "C" __declspec(dllimport) void __stdcall InitializeCriticalSection(::_RTL_CRITICAL_SECTION *);  
#endif  
```  
with this:  
```  
extern "C" __declspec(dllimport) void __stdcall InitializeCriticalSection(::_RTL_CRITICAL_SECTION *);  
```  
  
and replace this:  
```  
#if BOOST_PLAT_WINDOWS_RUNTIME  
        boost::signals2::detail::InitializeCriticalSectionEx(reinterpret_cast< boost::signals2::detail::rtl_critical_section* >(&cs_), 4000, 0);  
#else  
        boost::signals2::detail::InitializeCriticalSection(reinterpret_cast< boost::signals2::detail::rtl_critical_section* >(&cs_));   
#endif  
```  
with this:  
```  
        boost::signals2::detail::InitializeCriticalSection(reinterpret_cast< boost::signals2::detail::rtl_critical_section* >(&cs_));   
```  
  
Could someone have a look at this to find out what the issue is and who is at fault here?  
  
Thank you very much in advance and have an awesome day! :)  
  
Kind regards  
JuliusZet

---

## Comment 1

> Username: nilay2207  
> Created at: 2023-04-23 08:17:28 UTC  
> Url: https://github.com/boostorg/signals2/issues/67#issuecomment-1519070770  

It seems to be a bug in the library itself. However, there are a few things you can try to work around the issue:  
  
Use a different version of Boost: It's possible that this bug has been fixed in a later version of the library. You can try updating your Boost installation and see if the problem persists.  
  
Use a different library: If you can't get Boost.Asio to work, you can try using a different networking library, such as libuv or Simple DirectMedia Layer (SDL).  
  
Modify your code: Depending on the specifics of your program, there may be ways to modify your code to work around the issue. For example, you could try using a different threading model or restructuring your code to avoid using "io_service::run()".  
  
Contact the Boost developers: If none of the above solutions work, you can try contacting the developers of Boost and reporting the issue to them. They may be able to provide a more detailed explanation of the problem and offer a fix or workaround. You can do this by creating a new issue on the Boost GitHub repository or by posting a message to the Boost mailing list.

---

## Comment 2

> Username: fmhess  
> Created at: 2023-04-25 15:46:24 UTC  
> Url: https://github.com/boostorg/signals2/issues/67#issuecomment-1522027630  

I think this issue is fixed in the develop branch of Boost.Signals2 in commit 2ecf1b53bc970dd2b5e5d0f36fe8adf5d2181638 let me know if it works for you.

---

## Comment 3

> Username: fmhess  
> Created at: 2023-04-29 02:42:12 UTC  
> Url: https://github.com/boostorg/signals2/issues/67#issuecomment-1528531119  

merged to master
