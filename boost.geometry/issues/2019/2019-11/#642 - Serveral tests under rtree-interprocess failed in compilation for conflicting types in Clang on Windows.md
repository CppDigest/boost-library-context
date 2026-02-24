# #642 - Serveral tests under rtree/interprocess failed in compilation for conflicting types in Clang on Windows [Closed]

> Username: yuxianch  
> Created at: 2019-11-18 07:48:32 UTC  
> Updated at: 2019-11-19 02:25:54 UTC  
> Closed at: 2019-11-18 13:56:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/642  

Hi,  
I found that serveral tests under [rtree/interprocess](https://github.com/boostorg/geometry/tree/develop/index/test/rtree/interprocess) failed in compilation for conflicting types in MSVC. Take [rtree_interprocess_linear_dyn.cpp](https://github.com/boostorg/geometry/tree/develop/index/test/rtree/interprocess/rtree_interprocess_linear_dyn.cpp) as an example. When executing below command,  
```  
clang-cl rtree\interprocess\rtree_interprocess_linear_dyn.cpp -o rtree_interprocess_linear_dyn -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_NO_AUTO_PTR -DBOOST_THREAD_BUILD_DLL=1 -DBOO  
ST_THREAD_USE_DLL=1 -DBOOST_THREAD_WIN32 -DBOOST_TIMER_DYN_LINK=1 -I"." -I"..\..\..\.." -I"..\..\..\..\boost\geometry\extensions\contrib\ttmath" -I"..\..\test"  
```  
it will get many errors like below.   
```  
In file included from rtree/interprocess/rtree_interprocess_linear_dyn.cpp:10:  
In file included from .\rtree/interprocess/test_interprocess.hpp:15:  
In file included from ../../../..\boost/interprocess/managed_shared_memory.hpp:25:  
In file included from ../../../..\boost/interprocess/detail/managed_memory_impl.hpp:27:  
In file included from ../../../..\boost/interprocess/detail/os_file_functions.hpp:24:  
In file included from ../../../..\boost/interprocess/errors.hpp:41:  
../../../..\boost/interprocess/detail/win32_api.hpp:899:48: error: conflicting types for 'GetProcessTimes'  
extern "C" __declspec(dllimport) int __stdcall GetProcessTimes  
                                               ^  
c:/Program files (x86)/Windows Kits/10/include/10.0.18362.0/um\processthreadsapi.h:106:1: note: previous declaration is here  
GetProcessTimes(  
^  
In file included from rtree/interprocess/rtree_interprocess_linear_dyn.cpp:10:  
In file included from .\rtree/interprocess/test_interprocess.hpp:15:  
In file included from ../../../..\boost/interprocess/managed_shared_memory.hpp:25:  
In file included from ../../../..\boost/interprocess/detail/managed_memory_impl.hpp:27:  
In file included from ../../../..\boost/interprocess/detail/os_file_functions.hpp:24:  
In file included from ../../../..\boost/interprocess/errors.hpp:41:  
../../../..\boost/interprocess/detail/win32_api.hpp:915:49: error: functions that differ only in their return type cannot be overloaded  
extern "C" __declspec(dllimport) long __stdcall GetFileType(void *hFile);  
                                                ^  
c:/Program files (x86)/Windows Kits/10/include/10.0.18362.0/um\fileapi.h:690:1: note: previous declaration is here  
GetFileType(  
^  
```  
It's clear that some functions are also defined in the Windows headers and these headers are included, which causes this issue.   
To avoid this issue, I have tried to modify environmental variable `INCLUDE` and make it not to include path `c:/Program files (x86)/Windows Kits/10/include/10.0.18362.0/um`. However, this operation will bring another error in compiling [test/include/boost/test/utils/setcolor.hpp](https://github.com/boostorg/test/blob/develop/include/boost/test/utils/setcolor.hpp) for not finding `Windows.h`. Do you have some ideas to fix this issue?

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-11-18 13:48:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/642#issuecomment-555022507  

You are compiling with `clang-cl` so this is not MSVC but Clang on Windows. Is that correct?  
It looks like this issue is located in Boost.Interprocess, not in Boost.Geometry, do you agree?  
  
I do not see it failing in our regression matrix for any tested MSVC (https://www.boost.org/development/tests/develop/developer/geometry-index.html)  
Clang on Windows is not tested unfortunately.  
  
> Do you have some ideas to fix this issue?  
  
Yes, if I understand correctly, the declarations in Boost.Interprocess are incompatible with functions in Windows SDK and while MSVC is ok with it Clang is not. Ideally you could prepare a pull request here: https://github.com/boostorg/interprocess or at least report an Issue, but with pull request it will be faster.  
  
In short the WinApi functions declarations are wrong in this file:  
https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/detail/win32_api.hpp  
Either the types in a declaration should be changed or some #define is not set properly or is set improperly because the library assumes that the compiler is MSVC.

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-11-18 13:50:09 UTC  
> Updated at: 2019-11-18 13:59:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/642#issuecomment-555022937  

`GetFileType` should return `unsigned long`  
https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/detail/win32_api.hpp#L914  
  
[1] https://docs.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-getfiletype

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-11-18 13:55:30 UTC  
> Updated at: 2019-11-18 13:59:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/642#issuecomment-555025060  

`GetProcessTimes`  
https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/detail/win32_api.hpp#L898  
should take `FILETIME *`  
https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/detail/win32_api.hpp#L706  
and not Boost.Inteprocess implementation  
https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/detail/win32_api.hpp#L555  
  
AFAIU this is by design because it depends on `BOOST_USE_WINDOWS_H` which is not set for you. I don't know the reason.  
  
[1] https://docs.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-getprocesstimes

---

## Comment 4

> Username: awulkiew  
> Created at: 2019-11-18 13:56:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/642#issuecomment-555025338  

Anyway, this is not a bug in Boost.Geometry, so I'm closing it.

---

## Comment 5

> Username: yuxianch  
> Created at: 2019-11-19 02:25:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/642#issuecomment-555302572  

Wow. Thank you so much, @awulkiew . I will turn to Boost.Interprocess.
