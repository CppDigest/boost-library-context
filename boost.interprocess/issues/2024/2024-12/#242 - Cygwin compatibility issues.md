# #242 - Cygwin compatibility issues [Closed]

> Username: Lastique  
> Created at: 2024-12-16 09:36:43 UTC  
> Updated at: 2025-12-16 09:17:33 UTC  
> Closed at: 2025-12-16 06:23:44 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242  

Compiling Boost.Log with inter-process utilities (using Boost.Interprocess internally) on Cygwin fails, see [here](https://ci.appveyor.com/project/Lastique/log/builds/51171794/job/8car0jn3c9nsdaky#L746) and [here](https://github.com/boostorg/log/actions/runs/12362151114/job/34500869172#step:4:325):  
  
```  
In file included from ./boost/interprocess/errors.hpp:40:0,  
                 from ./boost/interprocess/exceptions.hpp:24,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:51:  
./boost/interprocess/detail/win32_api.hpp: In function 'int boost::interprocess::winapi::release_semaphore(void*, long int, long int*)':  
./boost/interprocess/detail/win32_api.hpp:778:61: error: cannot convert 'long int*' to 'LPLONG {aka int*}' for argument '3' to 'WINBOOL ReleaseSemaphore(HANDLE, LONG, LPLONG)'  
 {  return ReleaseSemaphore(handle, release_count, prev_count); }  
                                                             ^  
./boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::virtual_protect(void*, std::size_t, long unsigned int, long unsigned int&)':  
./boost/interprocess/detail/win32_api.hpp:860:101: error: cannot convert 'long unsigned int*' to 'boost::winapi::PDWORD_ {aka unsigned int*}' for argument '4' to 'boost::winapi::BOOL_ boost::ipwinapiext::VirtualProtect(boost::winapi::LPVOID_, boost::winapi::SIZE_T_, boost::winapi::DWORD_, boost::winapi::PDWORD_)'  
 {  return 0 != boost::ipwinapiext::VirtualProtect(base_addr, numbytes, flNewProtect, &lpflOldProtect); }  
                                                                                                     ^  
./boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::write_file(void*, const void*, long unsigned int, long unsigned int*, boost::interprocess::winapi::interprocess_overlapped*)':  
./boost/interprocess/detail/win32_api.hpp:905:95: error: cannot convert 'long unsigned int*' to 'boost::winapi::LPDWORD_ {aka unsigned int*}' for argument '4' to 'boost::winapi::BOOL_ boost::winapi::WriteFile(boost::winapi::HANDLE_, boost::winapi::LPCVOID_, boost::winapi::DWORD_, boost::winapi::LPDWORD_, boost::winapi::OVERLAPPED_*)'  
 {  return 0 != boost::winapi::WriteFile(hnd, buffer, bytes_to_write, bytes_written, overlapped);  }  
                                                                                               ^  
./boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::read_file(void*, void*, long unsigned int, long unsigned int*, boost::interprocess::winapi::interprocess_overlapped*)':  
./boost/interprocess/detail/win32_api.hpp:908:90: error: cannot convert 'long unsigned int*' to 'boost::winapi::LPDWORD_ {aka unsigned int*}' for argument '4' to 'boost::winapi::BOOL_ boost::winapi::ReadFile(boost::winapi::HANDLE_, boost::winapi::LPVOID_, boost::winapi::DWORD_, boost::winapi::LPDWORD_, boost::winapi::OVERLAPPED_*)'  
 {  return 0 != boost::winapi::ReadFile(hnd, buffer, bytes_to_read, bytes_read, overlapped);  }  
                                                                                          ^  
./boost/interprocess/detail/win32_api.hpp: In function 'long int boost::interprocess::winapi::reg_query_value_ex(boost::interprocess::winapi::hkey, const char*, long unsigned int*, long unsigned int*, unsigned char*, long unsigned int*)':  
./boost/interprocess/detail/win32_api.hpp:952:103: error: cannot convert 'long unsigned int*' to 'boost::winapi::DWORD_* {aka unsigned int*}' for argument '3' to 'boost::ipwinapiext::LSTATUS boost::ipwinapiext::RegQueryValueExA(boost::ipwinapiext::HKEY_, const char*, boost::winapi::DWORD_*, boost::winapi::DWORD_*, boost::winapi::BYTE_*, boost::winapi::DWORD_*)'  
 {  return boost::ipwinapiext::RegQueryValueExA(hKey, lpValueName, lpReserved, lpType, lpData, lpcbData); }  
                                                                                                       ^  
./boost/interprocess/detail/win32_api.hpp: In function 'long int boost::interprocess::winapi::reg_query_value_ex(boost::interprocess::winapi::hkey, const wchar_t*, long unsigned int*, long unsigned int*, unsigned char*, long unsigned int*)':  
./boost/interprocess/detail/win32_api.hpp:955:103: error: cannot convert 'long unsigned int*' to 'boost::winapi::DWORD_* {aka unsigned int*}' for argument '3' to 'boost::ipwinapiext::LSTATUS boost::ipwinapiext::RegQueryValueExW(boost::ipwinapiext::HKEY_, const wchar_t*, boost::winapi::DWORD_*, boost::winapi::DWORD_*, boost::winapi::BYTE_*, boost::winapi::DWORD_*)'  
 {  return boost::ipwinapiext::RegQueryValueExW(hKey, lpValueName, lpReserved, lpType, lpData, lpcbData); }  
                                                                                                       ^  
```  
  
Cygwin has LP64 ABI where `long` is 64-bit, not 32-bit like on native Windows. See [here](https://en.wikipedia.org/wiki/64-bit_computing#64-bit_data_models).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-11-21 22:59:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3564916365  

Cygwin calling win32 api is not supported by Interprocess, as it treats Cygwin as a Unix operating system. _XOPEN_SOURCE=600 or _POSIX_C_SOURCE might be needed when compiling so that needed system calls are visible to Boost.Interprocess.

---

## Comment 2

> Username: Lastique  
> Created at: 2025-11-22 00:00:56 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3565037295  

> Cygwin calling win32 api is not supported by Interprocess, as it treats Cygwin as a Unix operating system. _XOPEN_SOURCE=600 or _POSIX_C_SOURCE might be needed when compiling so that needed system calls are visible to Boost.Interprocess.  
  
The errors in the original post are produced by Boost.Interprocess because it treats Cygwin as a Win32 platform. It defines `BOOST_INTERPROCESS_WINDOWS` internally and includes boost/interprocess/detail/win32_api.hpp, which fails because of the ABI differences.  
  
If you intend to treat Cygwin as a POSIX platform, Boost.Interprocess should not define `BOOST_INTERPROCESS_WINDOWS` and should not include boost/interprocess/detail/win32_api.hpp internally but instead should use POSIX headers. However, I'm not sure how efficient and complete is the emulation of POSIX features like shared memory in Cygwin. It would seem using Win32 API directly should be more efficient and a safe bet, but the choice is yours, of course.  
  
In any case, Boost.Interprocess fails to compile for its internal reasons (either incorrect API is chosen, or WinAPI declarations are incorrect), so please, reopen.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2025-11-22 19:20:30 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3566983583  

Probably the bug is related to an old Boost.Interprocess version as recent Boost.Interprocess versions don't define BOOST_INTERPROCESS_WINDOWS under Cygwin. All Interprocess tests pass under cygwin using without using Win32 API.

---

## Comment 4

> Username: Lastique  
> Created at: 2025-11-24 17:03:10 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3571819098  

> Probably the bug is related to an old Boost.Interprocess version as recent Boost.Interprocess versions don't define BOOST_INTERPROCESS_WINDOWS under Cygwin. All Interprocess tests pass under cygwin using without using Win32 API.  
  
Nope, Boost.Log tests still fail in Boost.Interprocess as it is using Win32 API. See [here](https://github.com/boostorg/log/actions/runs/19640547690/job/56242523884#step:4:595).

---

## Comment 5

> Username: igaztanaga  
> Created at: 2025-11-25 15:05:21 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3576113403  

Then, some other code is defining WIN32 or similar before including Interprocess. Maybe I should protect the detecion mechanism testing for Cygwin first. Reopening.

---

## Comment 6

> Username: Lastique  
> Created at: 2025-11-25 15:51:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3576338701  

> Then, some other code is defining WIN32 or similar before including Interprocess.  
  
This is quite possible since Cygwin provides Windows SDK headers, which can be included by various libraries. Boost.ASIO is one example. I also define `BOOST_USE_WINDOWS_H` on Cygwin because I want Windows SDK to be used instead of Boost.WinAPI definitions when Boost.Log is being built.

---

## Comment 7

> Username: igaztanaga  
> Created at: 2025-12-13 23:52:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3649939250  

Let's see if commit https://github.com/boostorg/interprocess/commit/918ddcd06ca5a13989186413c0767a82e0d96db0 fixes the issue...

---

## Comment 8

> Username: Lastique  
> Created at: 2025-12-14 12:18:20 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3650854975  

Thanks. GitHub Actions have [passed](https://github.com/boostorg/log/actions/runs/20199832126/job/57989181816) this time, so it looks promising. AppVeyor CI is still queued and it will probably take a while to run.

---

## Comment 9

> Username: Lastique  
> Created at: 2025-12-15 18:19:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3656992523  

AppVeyor CI also [passed](https://ci.appveyor.com/project/Lastique/log/builds/53235395). Looks like it's fixed. Thanks again.

---

## Comment 10

> Username: igaztanaga  
> Created at: 2025-12-16 06:23:44 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3659022976  

Great, thanks for your patience!

---

## Comment 11

> Username: Lastique  
> Created at: 2025-12-16 09:17:33 UTC  
> Url: https://github.com/boostorg/interprocess/issues/242#issuecomment-3659558745  

Please, also merge the fix to master.
