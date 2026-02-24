# #281 Fix build with cmake and MinGW [Merged]

> Username: yh-sb  
> Created at: 2021-08-23 20:27:40 UTC  
> Updated at: 2022-06-12 15:46:29 UTC  
> Merged at: 2022-06-12 15:46:24 UTC  
> Closed at: 2022-06-12 15:46:24 UTC  
> Url: https://github.com/boostorg/fiber/pull/281  

It fixes the following error during build using CMake and MinGW-w64:  
(it allows to build whole Boost successfully)  
```log  
[ 27%] Building CXX object libs/fiber/CMakeFiles/boost_fiber_numa.dir/src/numa/windows/pin_thread.cpp.obj  
C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp: In function 'void boost::fibers::numa::pin_thread(uint32_t)':  
C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:26:42: error: invalid conversion from 'HANDLE' {aka 'void*'} to 'std::thread::native_handle_type' {aka 'long long unsigned int'} [-fpermissive]  
   26 |     pin_thread( cpuid, ::GetCurrentThread() );  
      |                        ~~~~~~~~~~~~~~~~~~^~  
      |                                          |  
      |                                          HANDLE {aka void*}  
In file included from C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:7:  
C:/boost/libs/fiber/include/boost/fiber/numa/pin_thread.hpp:26:33: note:   initializing argument 2 of 'void boost::fibers::numa::pin_thread(uint32_t, std::thread::native_handle_type)'  
   26 | void pin_thread( std::uint32_t, std::thread::native_handle_type);  
      |                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from C:/boost/libs/config/include/boost/config.hpp:39,  
                 from C:/boost/libs/fiber/include/boost/fiber/numa/pin_thread.hpp:13,  
                 from C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:7:  
C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp: In function 'void boost::fibers::numa::pin_thread(uint32_t, std::thread::native_handle_type)':  
C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:40:57: error: invalid conversion from 'std::thread::native_handle_type' {aka 'long long unsigned int'} to 'HANDLE' {aka 'void*'} [-fpermissive]  
   40 |     if ( BOOST_UNLIKELY( 0 == ::SetThreadGroupAffinity( h, & affinity, nullptr) ) ) {  
      |                                                         ^  
      |                                                         |  
      |                                                         std::thread::native_handle_type {aka long long unsigned int}  
C:/boost/libs/config/include/boost/config/compiler/gcc.hpp:96:44: note: in definition of macro 'BOOST_UNLIKELY'  
   96 | #define BOOST_UNLIKELY(x) __builtin_expect(x, 0)  
      |                                            ^  
In file included from c:\mydevtools\mingw64\x86_64-w64-mingw32\include\winbase.h:30,  
                 from c:\mydevtools\mingw64\x86_64-w64-mingw32\include\windows.h:70,  
                 from C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:10:  
c:\mydevtools\mingw64\x86_64-w64-mingw32\include\processtopologyapi.h:22:60: note:   initializing argument 1 of 'WINBOOL SetThreadGroupAffinity(HANDLE, const GROUP_AFFINITY*, PGROUP_AFFINITY)'  
   22 |   WINBASEAPI WINBOOL WINAPI SetThreadGroupAffinity (HANDLE hThread, CONST GROUP_AFFINITY *GroupAffinity, PGROUP_AFFINITY PreviousGroupAffinity);  
      |                                                     ~~~~~~~^~~~~~~  
mingw32-make[2]: *** [libs\fiber\CMakeFiles\boost_fiber_numa.dir\build.make:76: libs/fiber/CMakeFiles/boost_fiber_numa.dir/src/numa/windows/pin_thread.cpp.obj] Error 1  
```  
  
Also see sibling issues, which are fixed by this merge request:  
* [Build error with cmake and gcc (MinGW)](https://github.com/boostorg/fiber/issues/280)  
* [error 1 while executing b2.exe](https://github.com/boostorg/fiber/issues/278)

---

## Comment 1

> Username: yh-sb  
> Created_at: 2021-09-19 12:25:40 UTC  
> Url: https://github.com/boostorg/fiber/pull/281#issuecomment-922465724  

Hello @olk, I hope you are doing well.  
What do you think about this MR? I would be very grateful for merge.

---

## Comment 2

> Username: egorpugin  
> Created_at: 2022-06-11 20:51:34 UTC  
> Url: https://github.com/boostorg/fiber/pull/281#issuecomment-1152995813  

Bump.  
Same for me.

---

## Comment 3

> Username: olk  
> Created_at: 2022-06-12 15:46:29 UTC  
> Url: https://github.com/boostorg/fiber/pull/281#issuecomment-1153211640  

ty

---
