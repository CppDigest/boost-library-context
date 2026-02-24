# #280 - Build error with cmake and gcc (MinGW) [Closed]

> Username: yh-sb  
> Created at: 2021-08-22 13:19:45 UTC  
> Updated at: 2021-08-22 19:06:25 UTC  
> Closed at: 2021-08-22 19:06:25 UTC  
> Url: https://github.com/boostorg/fiber/issues/280  

I am trying to build boost using cmake and GCC (11.2.0 MinGW-W64 x86_64-posix-seh).  
  
Here is error during building boost fiber:  
```  
[ 27%] Built target boost_fiber  
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
mingw32-make[1]: *** [CMakeFiles\Makefile2:2649: libs/fiber/CMakeFiles/boost_fiber_numa.dir/all] Error 2  
mingw32-make: *** [Makefile:135: all] Error 2  
```  
  
As I understood from other github tickets and forums, it's due to posix threads in MinGW, which is still boost issue.  
I have tried to do the same with MinGW 8.1.0 with win32 threads, same result.  
  
Also be aware about this [merge request](https://github.com/boostorg/context/pull/186) which fixes other build error with MinGW and cmake.  
  
**How to reproduce:**  
1. Download MinGW https://winlibs.com and setup PATH for it.  
2. Clone boost: `git clone --recursive https://github.com/boostorg/boost.git`  
3. Build boost using cmake:  
```batch  
cmake . -Bboost -G "MinGW Makefiles"  
cmake --build boost  
```  
  
*NOTE: there is no error when building with bootstrap and b2*  
  
Can you help with this?

---

## Comment 1

> Username: olk  
> Created at: 2021-08-22 19:03:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/280#issuecomment-903315555  

It is a buck in mingw.  
  
yhsb2k ***@***.***> schrieb am So., 22. Aug. 2021, 15:19:  
  
> I am trying to build boost using cmake and GCC (11.2.0 MinGW-W64  
> x86_64-posix-seh).  
>  
> Here is error during building boost fiber:  
>  
> [ 27%] Built target boost_fiber  
> [ 27%] Building CXX object libs/fiber/CMakeFiles/boost_fiber_numa.dir/src/numa/windows/pin_thread.cpp.obj  
> C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp: In function 'void boost::fibers::numa::pin_thread(uint32_t)':  
> C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:26:42: error: invalid conversion from 'HANDLE' {aka 'void*'} to 'std::thread::native_handle_type' {aka 'long long unsigned int'} [-fpermissive]  
>    26 |     pin_thread( cpuid, ::GetCurrentThread() );  
>       |                        ~~~~~~~~~~~~~~~~~~^~  
>       |                                          |  
>       |                                          HANDLE {aka void*}  
> In file included from C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:7:  
> C:/boost/libs/fiber/include/boost/fiber/numa/pin_thread.hpp:26:33: note:   initializing argument 2 of 'void boost::fibers::numa::pin_thread(uint32_t, std::thread::native_handle_type)'  
>    26 | void pin_thread( std::uint32_t, std::thread::native_handle_type);  
>       |                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
> In file included from C:/boost/libs/config/include/boost/config.hpp:39,  
>                  from C:/boost/libs/fiber/include/boost/fiber/numa/pin_thread.hpp:13,  
>                  from C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:7:  
> C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp: In function 'void boost::fibers::numa::pin_thread(uint32_t, std::thread::native_handle_type)':  
> C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:40:57: error: invalid conversion from 'std::thread::native_handle_type' {aka 'long long unsigned int'} to 'HANDLE' {aka 'void*'} [-fpermissive]  
>    40 |     if ( BOOST_UNLIKELY( 0 == ::SetThreadGroupAffinity( h, & affinity, nullptr) ) ) {  
>       |                                                         ^  
>       |                                                         |  
>       |                                                         std::thread::native_handle_type {aka long long unsigned int}  
> C:/boost/libs/config/include/boost/config/compiler/gcc.hpp:96:44: note: in definition of macro 'BOOST_UNLIKELY'  
>    96 | #define BOOST_UNLIKELY(x) __builtin_expect(x, 0)  
>       |                                            ^  
> In file included from c:\mydevtools\mingw64\x86_64-w64-mingw32\include\winbase.h:30,  
>                  from c:\mydevtools\mingw64\x86_64-w64-mingw32\include\windows.h:70,  
>                  from C:\boost\libs\fiber\src\numa\windows\pin_thread.cpp:10:  
> c:\mydevtools\mingw64\x86_64-w64-mingw32\include\processtopologyapi.h:22:60: note:   initializing argument 1 of 'WINBOOL SetThreadGroupAffinity(HANDLE, const GROUP_AFFINITY*, PGROUP_AFFINITY)'  
>    22 |   WINBASEAPI WINBOOL WINAPI SetThreadGroupAffinity (HANDLE hThread, CONST GROUP_AFFINITY *GroupAffinity, PGROUP_AFFINITY PreviousGroupAffinity);  
>       |                                                     ~~~~~~~^~~~~~~  
> mingw32-make[2]: *** [libs\fiber\CMakeFiles\boost_fiber_numa.dir\build.make:76: libs/fiber/CMakeFiles/boost_fiber_numa.dir/src/numa/windows/pin_thread.cpp.obj] Error 1  
> mingw32-make[1]: *** [CMakeFiles\Makefile2:2649: libs/fiber/CMakeFiles/boost_fiber_numa.dir/all] Error 2  
> mingw32-make: *** [Makefile:135: all] Error 2  
>  
> As I understood from other github tickets and forums, it's due to posix  
> threads in MinGW, with is still boost issue.  
> I have tried to do the same with MinGW 8.1.0 with win32 threads, same  
> result.  
>  
> *NOTE: there is no error when building with bootstrap and b2*  
>  
> Can you help with this?  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/issues/280>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAG7IQHNNLV3Z2PICZV52PLT6D2P3ANCNFSM5CS6LAPQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&utm_campaign=notification-email>  
> .  
>

---

## Comment 2

> Username: olk  
> Created at: 2021-08-22 19:06:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/280#issuecomment-903315905  

::GetCurrentThread() return Handle = void*  
But mingw defines std::thread::native_handle_type as long long unsigned int instead of Handle aka void*.
