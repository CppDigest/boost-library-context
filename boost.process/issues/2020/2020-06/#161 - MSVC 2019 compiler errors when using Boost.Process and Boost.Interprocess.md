# #161 - MSVC 2019 compiler errors when using Boost.Process and Boost.Interprocess [Open]

> Username: robert-git  
> Created at: 2020-06-04 10:43:36 UTC  
> Updated at: 2020-06-05 11:08:55 UTC  
> Url: https://github.com/boostorg/process/issues/161  

Microsoft Visual Studio Community 2019, Version 16.6.1.  
boost 1.70.0  
  
Compilation of the following program fails:  
```  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/process.hpp>  
  
int main()  
{  
    return 0;  
}  
```  
...with these errors:  
  
```  
1>------ Build started: Project: boost_interprocess_vs_boost_process, Configuration: Debug x64 ------  
1>boost_interprocess_vs_boost_process_main.cpp  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(54,1): error C2116: 'boost::interprocess::winapi::CreateFileA': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(931): message : see declaration of 'boost::interprocess::winapi::CreateFileA'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(54,1): error C2733: 'CreateFileA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(931,51): message : see declaration of 'boost::interprocess::winapi::CreateFileA'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(65,1): error C2116: 'boost::interprocess::winapi::FindFirstFileA': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(915): message : see declaration of 'boost::interprocess::winapi::FindFirstFileA'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(65,1): error C2733: 'FindFirstFileA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(915,50): message : see declaration of 'boost::interprocess::winapi::FindFirstFileA'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(68,1): error C2116: 'boost::interprocess::winapi::FindNextFileA': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(916): message : see declaration of 'boost::interprocess::winapi::FindNextFileA'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(68,1): error C2733: 'FindNextFileA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(916,50): message : see declaration of 'boost::interprocess::winapi::FindNextFileA'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(90,1): error C2116: 'boost::interprocess::winapi::GetFileInformationByHandle': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(963): message : see declaration of 'boost::interprocess::winapi::GetFileInformationByHandle'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(90,1): error C2733: 'GetFileInformationByHandle': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(963,50): message : see declaration of 'boost::interprocess::winapi::GetFileInformationByHandle'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(103,1): error C2116: 'boost::interprocess::winapi::LockFileEx': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(953): message : see declaration of 'boost::interprocess::winapi::LockFileEx'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(103,1): error C2733: 'LockFileEx': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(953,48): message : see declaration of 'boost::interprocess::winapi::LockFileEx'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(127,1): error C2116: 'boost::interprocess::winapi::UnlockFileEx': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(954): message : see declaration of 'boost::interprocess::winapi::UnlockFileEx'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(127,1): error C2733: 'UnlockFileEx': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(954,48): message : see declaration of 'boost::interprocess::winapi::UnlockFileEx'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(160,1): error C2116: 'boost::interprocess::winapi::ReadFile': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(956): message : see declaration of 'boost::interprocess::winapi::ReadFile'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(160,1): error C2733: 'ReadFile': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(956,48): message : see declaration of 'boost::interprocess::winapi::ReadFile'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(171,1): error C2116: 'boost::interprocess::winapi::WriteFile': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(955): message : see declaration of 'boost::interprocess::winapi::WriteFile'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(171,1): error C2733: 'WriteFile': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(955,48): message : see declaration of 'boost::interprocess::winapi::WriteFile'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(189,1): error C2116: 'boost::interprocess::winapi::GetFileSizeEx': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(937): message : see declaration of 'boost::interprocess::winapi::GetFileSizeEx'  
1>C:\Boost\boost_1_70_0\boost\winapi\file_management.hpp(189,1): error C2733: 'GetFileSizeEx': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(937,48): message : see declaration of 'boost::interprocess::winapi::GetFileSizeEx'  
1>Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
1>- add -D_WIN32_WINNT=0x0601 to the compiler command line; or  
1>- add _WIN32_WINNT=0x0601 to your project's Preprocessor Definitions.  
1>Assuming _WIN32_WINNT=0x0601 (i.e. Windows 7 target).  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\fileapi.h(59,1): error C2116: 'boost::interprocess::winapi::CreateDirectoryA': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(944): message : see declaration of 'boost::interprocess::winapi::CreateDirectoryA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\fileapi.h(59,1): error C2733: 'CreateDirectoryA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(944,48): message : see declaration of 'boost::interprocess::winapi::CreateDirectoryA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\fileapi.h(690,1): error C2371: 'boost::interprocess::winapi::GetFileType': redefinition; different basic types  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(914): message : see declaration of 'boost::interprocess::winapi::GetFileType'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\fileapi.h(690,1): error C2733: 'GetFileType': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(914,49): message : see declaration of 'boost::interprocess::winapi::GetFileType'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\fileapi.h(1150,1): error C2116: 'boost::interprocess::winapi::SetFilePointerEx': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(950): message : see declaration of 'boost::interprocess::winapi::SetFilePointerEx'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\fileapi.h(1150,1): error C2733: 'SetFilePointerEx': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(950,48): message : see declaration of 'boost::interprocess::winapi::SetFilePointerEx'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\fileapi.h(1462,1): error C2371: 'boost::interprocess::winapi::GetTempPathA': redefinition; different basic types  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(946): message : see declaration of 'boost::interprocess::winapi::GetTempPathA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\fileapi.h(1462,1): error C2733: 'GetTempPathA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(946,48): message : see declaration of 'boost::interprocess::winapi::GetTempPathA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\synchapi.h(428,1): error C2116: 'boost::interprocess::winapi::CreateMutexA': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(920): message : see declaration of 'boost::interprocess::winapi::CreateMutexA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\synchapi.h(428,1): error C2733: 'CreateMutexA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(920,51): message : see declaration of 'boost::interprocess::winapi::CreateMutexA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\processthreadsapi.h(106,1): error C2116: 'boost::interprocess::winapi::GetProcessTimes': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(898): message : see declaration of 'boost::interprocess::winapi::GetProcessTimes'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\processthreadsapi.h(106,1): error C2733: 'GetProcessTimes': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(898,48): message : see declaration of 'boost::interprocess::winapi::GetProcessTimes'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\sysinfoapi.h(95,1): error C2116: 'boost::interprocess::winapi::GetSystemInfo': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(932): message : see declaration of 'boost::interprocess::winapi::GetSystemInfo'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\sysinfoapi.h(95,1): error C2733: 'GetSystemInfo': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(932,49): message : see declaration of 'boost::interprocess::winapi::GetSystemInfo'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\memoryapi.h(273,1): error C2116: 'boost::interprocess::winapi::FlushViewOfFile': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(933): message : see declaration of 'boost::interprocess::winapi::FlushViewOfFile'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\memoryapi.h(273,1): error C2733: 'FlushViewOfFile': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(933,48): message : see declaration of 'boost::interprocess::winapi::FlushViewOfFile'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\memoryapi.h(282,1): error C2116: 'boost::interprocess::winapi::UnmapViewOfFile': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(924): message : see declaration of 'boost::interprocess::winapi::UnmapViewOfFile'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\memoryapi.h(282,1): error C2733: 'UnmapViewOfFile': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(924,48): message : see declaration of 'boost::interprocess::winapi::UnmapViewOfFile'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\libloaderapi.h(186,1): error C2116: 'boost::interprocess::winapi::FreeLibrary': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(960): message : see declaration of 'boost::interprocess::winapi::FreeLibrary'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\libloaderapi.h(186,1): error C2733: 'FreeLibrary': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(960,50): message : see declaration of 'boost::interprocess::winapi::FreeLibrary'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\libloaderapi.h(260,1): error C2371: 'boost::interprocess::winapi::GetModuleHandleA': redefinition; different basic types  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(962): message : see declaration of 'boost::interprocess::winapi::GetModuleHandleA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\libloaderapi.h(260,1): error C2733: 'GetModuleHandleA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(962,52): message : see declaration of 'boost::interprocess::winapi::GetModuleHandleA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\libloaderapi.h(342,1): error C2116: 'boost::interprocess::winapi::GetProcAddress': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(961): message : see declaration of 'boost::interprocess::winapi::GetProcAddress'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\libloaderapi.h(342,1): error C2733: 'GetProcAddress': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(961,54): message : see declaration of 'boost::interprocess::winapi::GetProcAddress'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\libloaderapi.h(663,1): error C2371: 'boost::interprocess::winapi::LoadLibraryA': redefinition; different basic types  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(959): message : see declaration of 'boost::interprocess::winapi::LoadLibraryA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\libloaderapi.h(663,1): error C2733: 'LoadLibraryA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(959,52): message : see declaration of 'boost::interprocess::winapi::LoadLibraryA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\securitybaseapi.h(1054,1): error C2116: 'boost::interprocess::winapi::InitializeSecurityDescriptor': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(957): message : see declaration of 'boost::interprocess::winapi::InitializeSecurityDescriptor'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\securitybaseapi.h(1054,1): error C2733: 'InitializeSecurityDescriptor': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(957,48): message : see declaration of 'boost::interprocess::winapi::InitializeSecurityDescriptor'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\securitybaseapi.h(1390,1): error C2116: 'boost::interprocess::winapi::SetSecurityDescriptorDacl': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(958): message : see declaration of 'boost::interprocess::winapi::SetSecurityDescriptorDacl'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\securitybaseapi.h(1390,1): error C2733: 'SetSecurityDescriptorDacl': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(958,48): message : see declaration of 'boost::interprocess::winapi::SetSecurityDescriptorDacl'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winbase.h(3098,1): error C2116: 'boost::interprocess::winapi::CreateSemaphoreA': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(925): message : see declaration of 'boost::interprocess::winapi::CreateSemaphoreA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winbase.h(3098,1): error C2733: 'CreateSemaphoreA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(925,51): message : see declaration of 'boost::interprocess::winapi::CreateSemaphoreA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winbase.h(3208,1): error C2116: 'boost::interprocess::winapi::CreateFileMappingA': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(928): message : see declaration of 'boost::interprocess::winapi::CreateFileMappingA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winbase.h(3208,1): error C2733: 'CreateFileMappingA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(928,51): message : see declaration of 'boost::interprocess::winapi::CreateFileMappingA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winreg.h(212,1): error C2116: 'boost::interprocess::winapi::RegCloseKey': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(968): message : see declaration of 'boost::interprocess::winapi::RegCloseKey'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winreg.h(212,1): error C2733: 'RegCloseKey': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(968,49): message : see declaration of 'boost::interprocess::winapi::RegCloseKey'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winreg.h(692,1): error C2116: 'boost::interprocess::winapi::RegOpenKeyExA': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(966): message : see declaration of 'boost::interprocess::winapi::RegOpenKeyExA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winreg.h(692,1): error C2733: 'RegOpenKeyExA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(966,49): message : see declaration of 'boost::interprocess::winapi::RegOpenKeyExA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winreg.h(848,1): error C2116: 'boost::interprocess::winapi::RegQueryValueExA': function parameter lists do not match between declarations  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(967): message : see declaration of 'boost::interprocess::winapi::RegQueryValueExA'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winreg.h(848,1): error C2733: 'RegQueryValueExA': you cannot overload a function with 'extern "C"' linkage  
1>C:\Boost\boost_1_70_0\boost\interprocess\detail\win32_api.hpp(967,49): message : see declaration of 'boost::interprocess::winapi::RegQueryValueExA'  
1>C:\Boost\boost_1_70_0\boost\process\detail\windows\wait_group.hpp(30,70): warning C4244: 'argument': conversion from 'std::chrono::system_clock::rep' to 'boost::winapi::DWORD_', possible loss of data  
1>Done building project "boost_interprocess_vs_boost_process.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-06-04 11:11:35 UTC  
> Url: https://github.com/boostorg/process/issues/161#issuecomment-638783136  

That looks like a conflict between winapi & inteprocess.   
Can you try to `#define BOOST_USE_WINDOWS_H` before including process?

---

## Comment 2

> Username: robert-git  
> Created at: 2020-06-04 11:24:36 UTC  
> Updated at: 2020-06-04 11:25:24 UTC  
> Url: https://github.com/boostorg/process/issues/161#issuecomment-638788742  

Attempt 1 of 4 (failed):  
```  
#define BOOST_USE_WINDOWS_H  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/process.hpp>  
  
int main()  
{  
    return 0;  
}  
```  
Compiler output:  
```  
1>------ Build started: Project: boost_interprocess_vs_boost_process, Configuration: Debug x64 ------  
1>boost_interprocess_vs_boost_process_main.cpp  
1>C:\Boost\boost_1_70_0\boost\asio\detail\socket_types.hpp(24,1): fatal error C1189: #error:  WinSock.h has already been included  
1>Done building project "boost_interprocess_vs_boost_process.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```  
---  
Attempt 2 of 4 (failed):  
```  
#include <boost/interprocess/managed_shared_memory.hpp>  
#define BOOST_USE_WINDOWS_H  
#include <boost/process.hpp>  
  
int main()  
{  
    return 0;  
}  
```  
Compiler output:  
```  
1>------ Build started: Project: boost_interprocess_vs_boost_process, Configuration: Debug x64 ------  
1>boost_interprocess_vs_boost_process_main.cpp  
1>C:\Boost\boost_1_70_0\boost\winapi\get_last_error.hpp(26,9): error C2039: 'GetLastError': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\get_last_error.hpp(26,1): error C2873: 'GetLastError': symbol cannot be used in a using-declaration  
1>C:\Boost\boost_1_70_0\boost\process\detail\config.hpp(70,45): error C2039: 'GetLastError': is not a member of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\winapi\get_last_error.hpp(25): message : see declaration of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\process\detail\config.hpp(70,57): error C3861: 'GetLastError': identifier not found  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(58,66): error C2065: 'FORMAT_MESSAGE_ALLOCATE_BUFFER': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(58,64): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(58,64): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(61,66): error C2065: 'FORMAT_MESSAGE_IGNORE_INSERTS': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(61,63): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(61,63): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(62,66): error C2065: 'FORMAT_MESSAGE_FROM_STRING': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(62,60): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(62,60): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(63,66): error C2065: 'FORMAT_MESSAGE_FROM_HMODULE': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(63,61): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(63,61): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(64,66): error C2065: 'FORMAT_MESSAGE_FROM_SYSTEM': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(64,60): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(64,60): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(65,66): error C2065: 'FORMAT_MESSAGE_ARGUMENT_ARRAY': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(65,63): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(65,63): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(66,66): error C2065: 'FORMAT_MESSAGE_MAX_WIDTH_MASK': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(66,63): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(66,63): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(68,64): error C2065: 'LANG_NEUTRAL': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(68,45): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(68,45): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(69,64): error C2065: 'LANG_INVARIANT': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(69,47): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(69,47): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(71,64): error C2065: 'SUBLANG_DEFAULT': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(71,48): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(71,48): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(75,12): error C3861: 'MAKELANGID': identifier not found  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(73,41): error C3615: constexpr function 'boost::winapi::MAKELANGID_' cannot result in a constant expression  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(76): message : failure was caused by control reaching the end of a constexpr function  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(79,63): error C2065: 'SEM_FAILCRITICALERRORS': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(79,57): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(79,57): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(80,63): error C2065: 'SEM_NOGPFAULTERRORBOX': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(80,56): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(80,56): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(81,63): error C2065: 'SEM_NOALIGNMENTFAULTEXCEPT': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(81,61): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(81,61): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(82,63): error C2065: 'SEM_NOOPENFILEERRORBOX': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(82,57): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(82,57): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(117,9): error C2039: 'FormatMessageA': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(117,1): error C2873: 'FormatMessageA': symbol cannot be used in a using-declaration  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(119,9): error C2039: 'FormatMessageW': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(119,1): error C2873: 'FormatMessageW': symbol cannot be used in a using-declaration  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(121,9): error C2039: 'SetErrorMode': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(121,1): error C2873: 'SetErrorMode': symbol cannot be used in a using-declaration  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(134,14): error C2039: 'FormatMessageA': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(134,28): error C3861: 'FormatMessageA': identifier not found  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(147,14): error C2039: 'FormatMessageW': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\error_handling.hpp(147,28): error C3861: 'FormatMessageW': identifier not found  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(48,42): error C2065: 'CP_ACP': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(48,40): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(48,40): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(49,44): error C2065: 'CP_OEMCP': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(49,42): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(49,42): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(50,44): error C2065: 'CP_MACCP': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(50,42): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(50,42): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(51,49): error C2065: 'CP_THREAD_ACP': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(51,47): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(51,47): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(52,45): error C2065: 'CP_SYMBOL': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(52,43): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(52,43): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(53,43): error C2065: 'CP_UTF7': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(53,41): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(53,41): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(54,43): error C2065: 'CP_UTF8': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(54,41): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(54,41): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(56,51): error C2065: 'MB_PRECOMPOSED': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(56,49): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(56,49): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(57,49): error C2065: 'MB_COMPOSITE': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(57,47): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(57,47): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(58,53): error C2065: 'MB_USEGLYPHCHARS': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(58,51): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(58,51): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(59,57): error C2065: 'MB_ERR_INVALID_CHARS': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(59,55): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(59,55): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(61,54): error C2065: 'WC_COMPOSITECHECK': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(61,52): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(61,52): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(62,49): error C2065: 'WC_DISCARDNS': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(62,47): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(62,47): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(63,48): error C2065: 'WC_SEPCHARS': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(63,46): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(63,46): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(64,51): error C2065: 'WC_DEFAULTCHAR': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(64,49): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(64,49): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(66,57): error C2065: 'WC_NO_BEST_FIT_CHARS': undeclared identifier  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(66,55): error C2131: expression did not evaluate to a constant  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(66,55): message : a non-constant (sub-)expression was encountered  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(99,9): error C2039: 'MultiByteToWideChar': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(99,1): error C2873: 'MultiByteToWideChar': symbol cannot be used in a using-declaration  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(100,9): error C2039: 'WideCharToMultiByte': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\character_code_conversion.hpp(100,1): error C2873: 'WideCharToMultiByte': symbol cannot be used in a using-declaration  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(44,11): error C2039: 'HLOCAL': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(44,25): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(44,18): error C2146: syntax error: missing ';' before identifier 'HLOCAL_'  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(46,9): error C2039: 'LocalAlloc': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(46,1): error C2873: 'LocalAlloc': symbol cannot be used in a using-declaration  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(47,9): error C2039: 'LocalReAlloc': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(47,1): error C2873: 'LocalReAlloc': symbol cannot be used in a using-declaration  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(48,9): error C2039: 'LocalFree': is not a member of '`global namespace''  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(48,1): error C2873: 'LocalFree': symbol cannot be used in a using-declaration  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(94,40): error C2039: 'FormatMessageA': is not a member of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(42): message : see declaration of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(94,54): error C3861: 'FormatMessageA': identifier not found  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(115,40): error C2039: 'FormatMessageW': is not a member of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(42): message : see declaration of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(115,54): error C3861: 'FormatMessageW': identifier not found  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(127,32): error C2039: 'WideCharToMultiByte': is not a member of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(42): message : see declaration of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(127,51): error C3861: 'WideCharToMultiByte': identifier not found  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(128,28): error C2039: 'LocalFree': is not a member of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(42): message : see declaration of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(128,37): error C3861: 'LocalFree': identifier not found  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(157,24): error C2039: 'LocalFree': is not a member of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(42): message : see declaration of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(157,1): error C3861: 'LocalFree': identifier not found  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(173,36): error C2039: 'FormatMessageW': is not a member of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(42): message : see declaration of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(173,50): error C3861: 'FormatMessageW': identifier not found  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(193,28): error C2039: 'WideCharToMultiByte': is not a member of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\winapi\local_memory.hpp(42): message : see declaration of 'boost::winapi'  
1>C:\Boost\boost_1_70_0\boost\system\detail\system_category_win32.hpp(193,47): fatal error C1003: error count exceeds 100; stopping compilation  
1>Done building project "boost_interprocess_vs_boost_process.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
  
```  
---  
Attempt 3 of 4 (failed):  
```  
#define BOOST_USE_WINDOWS_H  
#include <boost/process.hpp>  
#include <boost/interprocess/managed_shared_memory.hpp>  
  
int main()  
{  
    return 0;  
}  
```  
Compiler output:  
```  
1>------ Build started: Project: boost_interprocess_vs_boost_process, Configuration: Debug x64 ------  
1>boost_interprocess_vs_boost_process_main.cpp  
1>C:\Boost\boost_1_70_0\boost\asio\detail\socket_types.hpp(24,1): fatal error C1189: #error:  WinSock.h has already been included  
1>Done building project "boost_interprocess_vs_boost_process.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```  
---  
Attempt 4 of 4 - Compiles!  
```  
#include <boost/process.hpp>  
#define BOOST_USE_WINDOWS_H  
#include <boost/interprocess/managed_shared_memory.hpp>  
  
int main()  
{  
    return 0;  
}  
````  
Compiler output:  
```  
1>------ Build started: Project: boost_interprocess_vs_boost_process, Configuration: Debug x64 ------  
1>boost_interprocess_vs_boost_process_main.cpp  
1>C:\Boost\boost_1_70_0\boost\process\detail\windows\wait_group.hpp(30,70): warning C4244: 'argument': conversion from 'std::chrono::system_clock::rep' to 'boost::winapi::DWORD_', possible loss of data  
1>boost_interprocess_vs_boost_process.vcxproj -> C:\Users\breadfish\source\repos\boost_interprocess_vs_boost_process\x64\Debug\boost_interprocess_vs_boost_process.exe  
1>Done building project "boost_interprocess_vs_boost_process.vcxproj".  
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 3

> Username: robert-git  
> Created at: 2020-06-04 11:58:26 UTC  
> Url: https://github.com/boostorg/process/issues/161#issuecomment-638802875  

Note to self on BOOST_USE_WINDOWS_H:  
https://www.boost.org/doc/libs/1_70_0/doc/html/interprocess/acknowledgements_notes.html#interprocess.acknowledgements_notes.notes_windows.boost_use_windows_h  
  
So I guess there are two requirements to make it work:  
  
1. Include `<boost/process.hpp>` at some point before `<boost/interprocess/managed_shared_memory.hpp>`.  
2. Define `BOOST_USE_WINDOWS_H` immediately before including `<boost/interprocess/managed_shared_memory.hpp>`.  
  
But I wonder how fragile this is?  In any case, I think I can proceed using this fix.  
  
Thank you for your help.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2020-06-05 07:11:53 UTC  
> Url: https://github.com/boostorg/process/issues/161#issuecomment-639301842  

The `boost/winapi` stuff is one huge workaround to avoid including `windows.h` in a header only library. If you define `BOOST_USE_WINDOWS_H` you disable that and tell `boost/winapi` to include `windows.h` and not define anything. So if you define it before any inclusion it should be stable.

---

## Comment 5

> Username: robert-git  
> Created at: 2020-06-05 11:08:55 UTC  
> Url: https://github.com/boostorg/process/issues/161#issuecomment-639414902  

> The `boost/winapi` stuff is one huge workaround to avoid including `windows.h` in a header only library. If you define **`BOOST_USE_WINDOWS_H`** you disable that and tell `boost/winapi` to include `windows.h` and not define anything. **So if you define it before any inclusion it should be stable.**  
  
[Attempt 1 in my post above](https://github.com/boostorg/process/issues/161#issuecomment-638788742) does just that, but it doesn't compile.
