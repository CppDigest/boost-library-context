# #60 - warning: cast between incompatible function types from boost::interprocess::winapi::farproc_t to boost::interprocess::winapi::QueryPerformanceFrequency_t [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 07:10:43 UTC  
> Updated at: 2018-09-20 04:20:46 UTC  
> Closed at: 2018-09-18 17:19:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/60  

This simple program:  
```  
#include <boost/interprocess/sync/file_lock.hpp>  
int main() {  
  return 0;  
}  
```  
cross-compiled from linux to Windows 64 bits with mingw-w64 + g++ 8.2.0 with the following command:  
`x86_64-w64-mingw32-g++-8.2.0 -o interprocess.o -c -O2 -DNDEBUG -Wall -Wextra -std=c++14 -I/softs/win64-mingw-8.2.0/release/boost/include interprocess.cpp`  
gives the following warnings:  
```  
In file included from /softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/errors.hpp:41,  
                 from /softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/exceptions.hpp:24,  
                 from /softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/sync/file_lock.hpp:24,  
                 from interprocess.cpp:1:  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::get_system_time_of_day_information(boost::interprocess::winapi::system_timeofday_information&)':  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:1676:58: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::NtQuerySystemInformation_t' {aka 'long int (*)(int, void*, long unsigned int, long unsigned int*)'} [-Wcast-function-type]  
          dll_func::get(dll_func::NtQuerySystemInformation);  
                                                          ^  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::unlink_file(const char*)':  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:1892:78: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::NtSetInformationFile_t' {aka 'long int (*)(void*, boost::interprocess::winapi::io_status_block_t*, void*, long unsigned int, int)'} [-Wcast-function-type]  
          (NtSetInformationFile_t)dll_func::get(dll_func::NtSetInformationFile);  
                                                                              ^  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:1894:94: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::NtQueryObject_t' {aka 'long int (*)(void*, boost::interprocess::winapi::object_information_class, void*, long unsigned int, long unsigned int*)'} [-Wcast-function-type]  
       NtQueryObject_t pNtQueryObject = (NtQueryObject_t)dll_func::get(dll_func::NtQueryObject);  
                                                                                              ^  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:1966:85: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::NtOpenFile_t' {aka 'long int (*)(void*, long unsigned int, boost::interprocess::winapi::object_attributes_t*, boost::interprocess::winapi::io_status_block_t*, long unsigned int, long unsigned int)'} [-Wcast-function-type]  
          NtOpenFile_t pNtOpenFile = (NtOpenFile_t)dll_func::get(dll_func::NtOpenFile);  
                                                                                     ^  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:1967:73: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::NtClose_t' {aka 'long int (*)(void*)'} [-Wcast-function-type]  
          NtClose_t pNtClose = (NtClose_t)dll_func::get(dll_func::NtClose);  
                                                                         ^  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::get_file_mapping_size(void*, long long int&)':  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:2413:63: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::NtQuerySection_t' {aka 'long int (*)(void*, boost::interprocess::winapi::section_information_class, boost::interprocess::winapi::interprocess_section_basic_information*, long unsigned int, long unsigned int*)'} [-Wcast-function-type]  
       (NtQuerySection_t)dll_func::get(dll_func::NtQuerySection);  
                                                               ^  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::get_semaphore_info(void*, long int&, long int&)':  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:2426:86: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::NtQuerySemaphore_t' {aka 'long int (*)(void*, unsigned int, boost::interprocess::winapi::interprocess_semaphore_basic_information*, unsigned int, unsigned int*)'} [-Wcast-function-type]  
          (winapi::NtQuerySemaphore_t)dll_func::get(winapi::dll_func::NtQuerySemaphore);  
                                                                                      ^  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::query_timer_resolution(long unsigned int*, long unsigned int*, long unsigned int*)':  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:2437:98: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::NtQueryTimerResolution_t' {aka 'long int (*)(long unsigned int*, long unsigned int*, long unsigned int*)'} [-Wcast-function-type]  
          (winapi::NtQueryTimerResolution_t)dll_func::get(winapi::dll_func::NtQueryTimerResolution);  
                                                                                                  ^  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::query_performance_counter(long long int*)':  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:2444:57: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::QueryPerformanceCounter_t' {aka 'int (*)(long long int*)'} [-Wcast-function-type]  
          dll_func::get(dll_func::QueryPerformanceCounter);  
                                                         ^  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp: In function 'bool boost::interprocess::winapi::query_performance_frequency(long long int*)':  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:2451:59: warning: cast between incompatible function types from 'boost::interprocess::winapi::farproc_t' {aka 'long long int (*)()'} to 'boost::interprocess::winapi::QueryPerformanceFrequency_t' {aka 'int (*)(long long int*)'} [-Wcast-function-type]  
          dll_func::get(dll_func::QueryPerformanceFrequency);  
                                                           ^  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-09-18 17:19:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/60#issuecomment-422476600  

Thanks for the report. Those warnings are harmless and unavoidable, so just disabled that warning in the win32 api header.

---

## Comment 2

> Username: kivadiu  
> Created at: 2018-09-19 03:38:58 UTC  
> Updated at: 2018-09-19 03:39:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/60#issuecomment-422639779  

Thanks for the commit. What about adding `#pragma GCC diagnostic push` at the beginning of the header and `#pragma GCC diagnostic pop` at the end to avoid removing warnings from user code?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2018-09-19 19:42:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/60#issuecomment-422933177  

If I'm not mistaken, that's what the patch does.

---

## Comment 4

> Username: kivadiu  
> Created at: 2018-09-20 04:20:46 UTC  
> Url: https://github.com/boostorg/interprocess/issues/60#issuecomment-423035447  

I'm sorry, yes it was there in the old code.  
Thanks again.
