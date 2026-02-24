# #76 - Cygwin compilation errors [Closed]

> Username: Lastique  
> Created at: 2019-01-06 15:48:06 UTC  
> Updated at: 2021-02-11 08:16:03 UTC  
> Closed at: 2021-02-10 21:29:03 UTC  
> Url: https://github.com/boostorg/interprocess/issues/76  

There are a number of compilation of Boost.Interprocess on Cygwin. On 32-bit Cygwin the errors are:  
  
```  
./boost/interprocess/detail/os_thread_functions.hpp: In function вЂint boost::interprocess::ipcdetail::thread_create(boost::interprocess::ipcdetail::OS_thread_t*, unsigned int (__attribute__((__stdcall__)) *)(void*), void*)вЂ™:  
./boost/interprocess/detail/os_thread_functions.hpp:524:21: error: вЂ_beginthreadexвЂ™ was not declared in this scope  
    void* h = (void*)_beginthreadex( 0, 0, start_routine, arg, 0, 0 );  
                     ^~~~~~~~~~~~~~  
```  
  
`_beginthreadex` is not a Windows API function, it is specific to MSVC RTL, which is not used on Cygwin. You can use pthreads on this platform.  
  
In addition to that, 64-bit Cygwin is a [LP64](https://en.wikipedia.org/wiki/64-bit_computing#64-bit_data_models) platform, meaning that `long` is a 64-bit integer on that platform. This means that a great deal of Windows SDK emulation layer in Boost.Interprocess is not ABI-compatible with that platform. This results in errors like these:  
  
```  
./boost/interprocess/detail/win32_api.hpp: In function ‘int boost::interprocess::winapi::release_semaphore(void*, long int, long int*)’:  
./boost/interprocess/detail/win32_api.hpp:1358:61: error: cannot convert ‘long int*’ to ‘LPLONG {aka int*}’ for argument ‘3’ to ‘WINBOOL ReleaseSemaphore(HANDLE, LONG, LPLONG)’  
 {  return ReleaseSemaphore(handle, release_count, prev_count); }  
                                                             ^  
./boost/interprocess/detail/win32_api.hpp: In function ‘bool boost::interprocess::winapi::virtual_protect(void*, std::size_t, long unsigned int, long unsigned int&)’:  
./boost/interprocess/detail/win32_api.hpp:1428:81: error: cannot convert ‘long unsigned int*’ to ‘PDWORD {aka unsigned int*}’ for argument ‘4’ to ‘WINBOOL VirtualProtect(LPVOID, SIZE_T, DWORD, PDWORD)’  
 {  return 0 != VirtualProtect(base_addr, numbytes, flNewProtect, &lpflOldProtect); }  
                                                                                 ^  
./boost/interprocess/detail/win32_api.hpp: In function ‘bool boost::interprocess::winapi::write_file(void*, const void*, long unsigned int, long unsigned int*, boost::interprocess::winapi::interprocess_overlapped*)’:  
./boost/interprocess/detail/win32_api.hpp:1464:80: error: cannot convert ‘long unsigned int*’ to ‘LPDWORD {aka unsigned int*}’ for argument ‘4’ to ‘WINBOOL WriteFile(HANDLE, LPCVOID, DWORD, LPDWORD, LPOVERLAPPED)’  
 {  return 0 != WriteFile(hnd, buffer, bytes_to_write, bytes_written, overlapped);  }  
                                                                                ^  
./boost/interprocess/detail/win32_api.hpp: In function ‘bool boost::interprocess::winapi::read_file(void*, void*, long unsigned int, long unsigned int*, boost::interprocess::winapi::interprocess_overlapped*)’:  
./boost/interprocess/detail/win32_api.hpp:1467:75: error: cannot convert ‘long unsigned int*’ to ‘LPDWORD {aka unsigned int*}’ for argument ‘4’ to ‘WINBOOL ReadFile(HANDLE, LPVOID, DWORD, LPDWORD, LPOVERLAPPED)’  
 {  return 0 != ReadFile(hnd, buffer, bytes_to_read, bytes_read, overlapped);  }  
                                                                           ^  
./boost/interprocess/detail/win32_api.hpp: In function ‘long int boost::interprocess::winapi::reg_query_value_ex(boost::interprocess::winapi::hkey, const char*, long unsigned int*, long unsigned int*, unsigned char*, long unsigned int*)’:  
./boost/interprocess/detail/win32_api.hpp:1507:83: error: cannot convert ‘long unsigned int*’ to ‘LPDWORD {aka unsigned int*}’ for argument ‘3’ to ‘LONG RegQueryValueExA(HKEY, LPCSTR, LPDWORD, LPDWORD, LPBYTE, LPDWORD)’  
 {  return RegQueryValueExA(hKey, lpValueName, lpReserved, lpType, lpData, lpcbData); }  
                                                                                   ^  
./boost/interprocess/detail/win32_api.hpp: In function ‘bool boost::interprocess::winapi::get_last_bootup_time(std::string&)’:  
./boost/interprocess/detail/win32_api.hpp:2341:40: error: cannot convert ‘long unsigned int*’ to ‘DWORD* {aka unsigned int*}’ for argument ‘6’ to ‘WINBOOL ReadEventLogA(HANDLE, DWORD, DWORD, LPVOID, DWORD, DWORD*, DWORD*)’  
                   &dwMinimumBytesToRead)) {  
                                        ^  
./boost/interprocess/detail/win32_api.hpp: In function вЂlong int boost::interprocess::winapi::interlocked_increment(volatile long int*)вЂ™:  
./boost/interprocess/detail/win32_api.hpp:1473:62: error: no matching function for call to вЂ_InterlockedIncrement(long int*)вЂ™  
 {  return BOOST_INTERLOCKED_INCREMENT(const_cast<long*>(addr));  }  
                                                              ^  
In file included from /usr/include/w32api/windows.h:70:0,  
                 from ./boost/winapi/basic_types.hpp:19,  
                 from ./boost/winapi/error_codes.hpp:11,  
                 from ./boost/system/detail/system_category_win32.hpp:12,  
                 from ./boost/system/error_code.hpp:905,  
                 from ./boost/log/exceptions.hpp:23,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:42:  
/usr/include/w32api/winbase.h:2937:29: note: candidate: long unsigned int _InterlockedIncrement(volatile long unsigned int*) <near match>  
   FORCEINLINE unsigned long InterlockedIncrement (unsigned long volatile *Addend) {  
                             ^  
/usr/include/w32api/winbase.h:2937:29: note:   conversion of argument 1 would be ill-formed:  
In file included from ./boost/interprocess/errors.hpp:41:0,  
                 from ./boost/interprocess/exceptions.hpp:24,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:49:  
./boost/interprocess/detail/win32_api.hpp:1473:39: error: invalid conversion from вЂlong int*вЂ™ to вЂvolatile long unsigned int*вЂ™ [-fpermissive]  
 {  return BOOST_INTERLOCKED_INCREMENT(const_cast<long*>(addr));  }  
                                       ^~~~~~~~~~~~~~~~~~~~~~~  
./boost/interprocess/detail/win32_api.hpp: In function вЂlong int boost::interprocess::winapi::interlocked_decrement(volatile long int*)вЂ™:  
./boost/interprocess/detail/win32_api.hpp:1476:62: error: no matching function for call to вЂ_InterlockedDecrement(long int*)вЂ™  
 {  return BOOST_INTERLOCKED_DECREMENT(const_cast<long*>(addr));  }  
                                                              ^  
In file included from /usr/include/w32api/windows.h:70:0,  
                 from ./boost/winapi/basic_types.hpp:19,  
                 from ./boost/winapi/error_codes.hpp:11,  
                 from ./boost/system/detail/system_category_win32.hpp:12,  
                 from ./boost/system/error_code.hpp:905,  
                 from ./boost/log/exceptions.hpp:23,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:42:  
/usr/include/w32api/winbase.h:2955:29: note: candidate: long unsigned int _InterlockedDecrement(volatile long unsigned int*) <near match>  
   FORCEINLINE unsigned long InterlockedDecrement (unsigned long volatile *Addend) {  
                             ^  
/usr/include/w32api/winbase.h:2955:29: note:   conversion of argument 1 would be ill-formed:  
In file included from ./boost/interprocess/errors.hpp:41:0,  
                 from ./boost/interprocess/exceptions.hpp:24,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:49:  
./boost/interprocess/detail/win32_api.hpp:1476:39: error: invalid conversion from вЂlong int*вЂ™ to вЂvolatile long unsigned int*вЂ™ [-fpermissive]  
 {  return BOOST_INTERLOCKED_DECREMENT(const_cast<long*>(addr));  }  
                                       ^~~~~~~~~~~~~~~~~~~~~~~  
./boost/interprocess/detail/win32_api.hpp: In function вЂlong int boost::interprocess::winapi::interlocked_compare_exchange(volatile long int*, long int, long int)вЂ™:  
./boost/interprocess/detail/win32_api.hpp:1479:81: error: no matching function for call to вЂ_InterlockedCompareExchange(long int*, long int&, long int&)вЂ™  
 {  return BOOST_INTERLOCKED_COMPARE_EXCHANGE(const_cast<long*>(addr), val1, val2);  }  
                                                                                 ^  
In file included from /usr/include/w32api/windows.h:70:0,  
                 from ./boost/winapi/basic_types.hpp:19,  
                 from ./boost/winapi/error_codes.hpp:11,  
                 from ./boost/system/detail/system_category_win32.hpp:12,  
                 from ./boost/system/error_code.hpp:905,  
                 from ./boost/log/exceptions.hpp:23,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:42:  
/usr/include/w32api/winbase.h:3025:29: note: candidate: long unsigned int _InterlockedCompareExchange(volatile long unsigned int*, long unsigned int, long unsigned int) <near match>  
   FORCEINLINE unsigned long InterlockedCompareExchange (unsigned long volatile *Destination, unsigned long Exchange, unsigned long Comperand) {  
                             ^  
/usr/include/w32api/winbase.h:3025:29: note:   conversion of argument 1 would be ill-formed:  
In file included from ./boost/interprocess/errors.hpp:41:0,  
                 from ./boost/interprocess/exceptions.hpp:24,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:49:  
./boost/interprocess/detail/win32_api.hpp:1479:46: error: invalid conversion from вЂlong int*вЂ™ to вЂvolatile long unsigned int*вЂ™ [-fpermissive]  
 {  return BOOST_INTERLOCKED_COMPARE_EXCHANGE(const_cast<long*>(addr), val1, val2);  }  
                                              ^~~~~~~~~~~~~~~~~~~~~~~  
./boost/interprocess/detail/win32_api.hpp: In function вЂlong int boost::interprocess::winapi::interlocked_exchange_add(volatile long int*, long int)вЂ™:  
./boost/interprocess/detail/win32_api.hpp:1482:74: error: no matching function for call to вЂ_InterlockedExchangeAdd(long int*, long int&)вЂ™  
 {  return BOOST_INTERLOCKED_EXCHANGE_ADD(const_cast<long*>(addend), value);  }  
                                                                          ^  
In file included from /usr/include/w32api/windows.h:70:0,  
                 from ./boost/winapi/basic_types.hpp:19,  
                 from ./boost/winapi/error_codes.hpp:11,  
                 from ./boost/system/detail/system_category_win32.hpp:12,  
                 from ./boost/system/error_code.hpp:905,  
                 from ./boost/log/exceptions.hpp:23,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:42:  
/usr/include/w32api/winbase.h:2995:29: note: candidate: long unsigned int _InterlockedExchangeAdd(volatile long unsigned int*, long unsigned int) <near match>  
   FORCEINLINE unsigned long InterlockedExchangeAdd (unsigned long volatile *Addend, unsigned long Value) {  
                             ^  
/usr/include/w32api/winbase.h:2995:29: note:   conversion of argument 1 would be ill-formed:  
In file included from ./boost/interprocess/errors.hpp:41:0,  
                 from ./boost/interprocess/exceptions.hpp:24,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:49:  
./boost/interprocess/detail/win32_api.hpp:1482:42: error: invalid conversion from вЂlong int*вЂ™ to вЂvolatile long unsigned int*вЂ™ [-fpermissive]  
 {  return BOOST_INTERLOCKED_EXCHANGE_ADD(const_cast<long*>(addend), value);  }  
                                          ^~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/interprocess/detail/win32_api.hpp: In function вЂlong int boost::interprocess::winapi::interlocked_exchange(volatile long int*, long int)вЂ™:  
./boost/interprocess/detail/win32_api.hpp:1485:70: error: no matching function for call to вЂ_InterlockedExchange(long int*, long int&)вЂ™  
 {  return BOOST_INTERLOCKED_EXCHANGE(const_cast<long*>(addend), value);  }  
                                                                      ^  
In file included from /usr/include/w32api/windows.h:70:0,  
                 from ./boost/winapi/basic_types.hpp:19,  
                 from ./boost/winapi/error_codes.hpp:11,  
                 from ./boost/system/detail/system_category_win32.hpp:12,  
                 from ./boost/system/error_code.hpp:905,  
                 from ./boost/log/exceptions.hpp:23,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:42:  
/usr/include/w32api/winbase.h:2973:29: note: candidate: long unsigned int _InterlockedExchange(volatile long unsigned int*, long unsigned int) <near match>  
   FORCEINLINE unsigned long InterlockedExchange (unsigned long volatile *Target, unsigned long Value) {  
                             ^  
/usr/include/w32api/winbase.h:2973:29: note:   conversion of argument 1 would be ill-formed:  
In file included from ./boost/interprocess/errors.hpp:41:0,  
                 from ./boost/interprocess/exceptions.hpp:24,  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:49:  
./boost/interprocess/detail/win32_api.hpp:1485:38: error: invalid conversion from вЂlong int*вЂ™ to вЂvolatile long unsigned int*вЂ™ [-fpermissive]  
 {  return BOOST_INTERLOCKED_EXCHANGE(const_cast<long*>(addend), value);  }  
                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/interprocess/detail/win32_api.hpp: In function вЂlong int boost::interprocess::winapi::reg_query_value_ex(boost::interprocess::winapi::hkey, const char*, long unsigned int*, long unsigned int*, unsigned char*, long unsigned int*)вЂ™:  
./boost/interprocess/detail/win32_api.hpp:1507:83: error: cannot convert вЂlong unsigned int*вЂ™ to вЂLPDWORD {aka unsigned int*}вЂ™ for argument вЂ3вЂ™ to вЂLONG RegQueryValueExA(HKEY, LPCSTR, LPDWORD, LPDWORD, LPBYTE, LPDWORD)вЂ™  
 {  return RegQueryValueExA(hKey, lpValueName, lpReserved, lpType, lpData, lpcbData); }  
                                                                                   ^  
./boost/interprocess/detail/win32_api.hpp: In function вЂbool boost::interprocess::winapi::get_last_bootup_time(std::string&)вЂ™:  
./boost/interprocess/detail/win32_api.hpp:2341:40: error: cannot convert вЂlong unsigned int*вЂ™ to вЂDWORD* {aka unsigned int*}вЂ™ for argument вЂ6вЂ™ to вЂWINBOOL ReadEventLogA(HANDLE, DWORD, DWORD, LPVOID, DWORD, DWORD*, DWORD*)вЂ™  
                   &dwMinimumBytesToRead)) {  
                                        ^  
```  
  
My advice would be to port the library to Boost.WinAPI, which already has workarounds in place for Cygwin64.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-10 21:29:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/76#issuecomment-777048575  

Many thanks for the report!

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-02-10 21:31:35 UTC  
> Url: https://github.com/boostorg/interprocess/issues/76#issuecomment-777049678  

Regarding the comment "In addition to that, 64-bit Cygwin is a LP64 platform, meaning that long is a 64-bit integer on that platform", isn't cygwin supposed to use the POSIX API?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-02-11 08:16:03 UTC  
> Url: https://github.com/boostorg/interprocess/issues/76#issuecomment-777269168  

Yes, but it also has support for WinAPI. That support is different from MSVC in that `long` size is 8 bytes, not 4.
