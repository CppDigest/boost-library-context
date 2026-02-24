# #172 - WIN64: 'invalid conversion from 'HANDLE {aka void*}' to 'std::thread::native_handle_type {aka long long unsigned int}'' errors for builds using mingw-w64 [Closed]

> Username: sav-ix  
> Created at: 2018-04-20 16:25:25 UTC  
> Updated at: 2018-05-15 19:44:28 UTC  
> Closed at: 2018-05-04 05:49:47 UTC  
> Url: https://github.com/boostorg/fiber/issues/172  

Hello, everyone,  
  
For Boost.Fiber builds using mingw-w64 got errors:  
```  
Junction created for boost\fiber <<===>> libs\fiber\include\boost\fiber  
gcc.compile.c++ bin.v2\libs\fiber\build\gcc-7.3.0\debug\threading-multi\numa\windows\pin_thread.o  
  
    "g++"   -m64 -mthreads -O0 -fno-inline -Wall -g  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -D_WIN32_WINNT=0x0601  -I"." -c -o "bin.v2\libs\fiber\build\gcc-7.3.0\debug\threading-multi\numa\windows\pin_thread.o" "libs\fiber\src\numa\windows\pin_thread.cpp"  
  
libs\fiber\src\numa\windows\pin_thread.cpp: In function 'void boost::fibers::numa::pin_thread(uint32_t)':  
libs\fiber\src\numa\windows\pin_thread.cpp:26:42: error: invalid conversion from 'HANDLE {aka void*}' to 'std::thread::native_handle_type {aka long long unsigned int}' [-fpermissive]  
     pin_thread( cpuid, ::GetCurrentThread() );  
                        ~~~~~~~~~~~~~~~~~~^~  
In file included from libs\fiber\src\numa\windows\pin_thread.cpp:7:0:  
./boost/fiber/numa/pin_thread.hpp:26:6: note:   initializing argument 2 of 'void boost::fibers::numa::pin_thread(uint32_t, std::thread::native_handle_type)'  
 void pin_thread( std::uint32_t, std::thread::native_handle_type);  
      ^~~~~~~~~~  
In file included from ./boost/config.hpp:39:0,  
                 from ./boost/fiber/numa/pin_thread.hpp:13,  
                 from libs\fiber\src\numa\windows\pin_thread.cpp:7:  
libs\fiber\src\numa\windows\pin_thread.cpp: In function 'void boost::fibers::numa::pin_thread(uint32_t, std::thread::native_handle_type)':  
libs\fiber\src\numa\windows\pin_thread.cpp:40:79: error: invalid conversion from 'std::thread::native_handle_type {aka long long unsigned int}' to 'HANDLE {aka void*}' [-fpermissive]  
     if ( BOOST_UNLIKELY( 0 == ::SetThreadGroupAffinity( h, & affinity, nullptr) ) ) {  
                                                                               ^  
./boost/config/compiler/gcc.hpp:96:44: note: in definition of macro 'BOOST_UNLIKELY'  
 #define BOOST_UNLIKELY(x) __builtin_expect(x, 0)  
                                            ^  
In file included from C:/ProgramData/Mingw64/x86_64-w64-mingw32/include/winbase.h:30:0,  
                 from C:/ProgramData/Mingw64/x86_64-w64-mingw32/include/windows.h:70,  
                 from libs\fiber\src\numa\windows\pin_thread.cpp:10:  
C:/ProgramData/Mingw64/x86_64-w64-mingw32/include/processtopologyapi.h:22:29: note:   initializing argument 1 of 'WINBOOL SetThreadGroupAffinity(HANDLE, const GROUP_AFFINITY*, PGROUP_AFFINITY)'  
   WINBASEAPI WINBOOL WINAPI SetThreadGroupAffinity (HANDLE hThread, CONST GROUP_AFFINITY *GroupAffinity, PGROUP_AFFINITY PreviousGroupAffinity);  
                             ^~~~~~~~~~~~~~~~~~~~~~  
...failed gcc.compile.c++ bin.v2\libs\fiber\build\gcc-7.3.0\debug\threading-multi\numa\windows\pin_thread.o...  
...failed updating 1 target...  
...updated 14333 targets...  
```  
  
Reproduced for:  
* builds using mingw-w64,  
  
not reproduced for:  
* builds using Windows ICC and MSVC.  
  
  
Environment:  
* Windows 10 x64,  
* mingw-w64 x86_64 7.3.0,  
* ICC 2018 Update 2,  
* MSVC 2017 15.5.0,  
* Windows SDK 10.0.16299.15,  
* MSYS2 20170918,  
* Boost-1.68.0-dev (f7c262f06a4b22f067968a773b63ec7dacec408a).  
  
  
Best,  
  
Alexander

---

## Comment 1

> Username: olk  
> Created at: 2018-04-28 05:40:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-385142679  

It's a bug in mingw:  
error: invalid conversion from 'HANDLE {aka void*}' to 'std::thread::native_handle_type {aka long long unsigned int}'  
  
std::thread::native_handle_type should be of type HANDLE (e.g. void*)

---

## Comment 2

> Username: jwakely  
> Created at: 2018-05-01 17:25:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-385731243  

> std::thread::native_handle_type should be of type HANDLE (e.g. void*)  
  
Why? Doesn't mingw-w64 use a Windows port of libpthread, and so the thread handle is a `pthread_t` not a native Windows thread handle?

---

## Comment 3

> Username: sav-ix  
> Created at: 2018-05-01 18:01:36 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-385741829  

Just in case, there are two threading implementations for mingw-w64:  
```  
threads-win32  
threads-posix  
```  
This issue relate to **posix** one, particularly [x86_64-7.3.0-release-posix-seh-rt_v5-rev0.7z](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/7.3.0/threads-posix/seh/x86_64-7.3.0-release-posix-seh-rt_v5-rev0.7z/download).

---

## Comment 4

> Username: jwakely  
> Created at: 2018-05-01 18:03:19 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-385742274  

Then it's not a mingw bug, it's a boost one. The type of `native_handle_type` is implementation-defined, and mingw-w64 using pthreads defines it to be the pthread handle. Boost.Fiber should not assume that just because the OS is Windows that `native_handle_type` is `HANDLE`.

---

## Comment 5

> Username: olk  
> Created at: 2018-05-02 06:05:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-385873618  

I assume that native_handle is equal to the native handle of the OS...  
I'm too busy in the moment - you are welcome to provide a patch

---

## Comment 6

> Username: olk  
> Created at: 2018-05-04 05:49:47 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-386510909  

MinGW using pthreads can not be supported because functions of the OS API like SetThreadGroupAffinity() are used. This functions require the handle type HANDLE (aka void*).

---

## Comment 7

> Username: sav-ix  
> Created at: 2018-05-11 07:41:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-388287131  

Since MinGW using win32 has threading issues ([1](https://sourceforge.net/p/mingw-w64/bugs/527/) [2](https://github.com/boostorg/config/commit/fe5e07b521e49f6cca712c801e025fed13c23979)), its use also doesn't please.

---

## Comment 8

> Username: olk  
> Created at: 2018-05-11 07:49:54 UTC  
> Updated at: 2018-05-11 07:50:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-388289037  

AFAIK, mingw-w64 (win64) seams to work

---

## Comment 9

> Username: sav-ix  
> Created at: 2018-05-11 07:59:33 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-388291154  

Mean `threads-win32` vs `threads-posix` implementation, not `i686` vs `x86_64` architecture.

---

## Comment 10

> Username: jwakely  
> Created at: 2018-05-11 10:35:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-388327417  

Yes, as I understand it the pthreads implementation is the more widely-used version of mingw-w64, so not supporting it is unfortunate.

---

## Comment 11

> Username: olk  
> Created at: 2018-05-11 11:14:41 UTC  
> Updated at: 2018-05-11 11:15:08 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-388335093  

For NUMA features I'm forced to use the OS specific APIs which require the OS native thread handle (for Windows it is HANDLE, aka void*).  
  
A solution would require to convert the std::thread::id to the native handle (I'm pretty sure that the majority of the users insist to use C++11 std::thread together with boost.fiber's NUMA-support). So the question is how do you convert (POSIX-thread) MinGWs std::thread::id to Windows HANDLE?  
  
I've no idea (requires to analyse the MinGW source code) ... unfortunately I've not the time at the moment to do that. But you are welcome to provide a patch.

---

## Comment 12

> Username: jwakely  
> Created at: 2018-05-11 11:46:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-388340826  

Completely understood, but surely it would be better to simply make those features absent for targets that can't support them, instead of failing the entire build?

---

## Comment 13

> Username: olk  
> Created at: 2018-05-11 12:11:36 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-388345928  

this would require a new b2 property like `numa=on` or move the NUMa related code into a new boost library

---

## Comment 14

> Username: olk  
> Created at: 2018-05-13 07:22:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-388606949  

NUMA support move to separate library - enabled by b2 property `numa=on`

---

## Comment 15

> Username: olk  
> Created at: 2018-05-13 07:29:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-388607320  

NUMA support move to separate library - enabled by b2 property `numa=on`  
Could you verify that the fix is working for you, please.

---

## Comment 16

> Username: sav-ix  
> Created at: 2018-05-15 19:44:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/172#issuecomment-389289822  

Updated Boost-1.68.0-dev sources to recent version (f2760d4e28092fa2cb7babd1561ec1164e098b25), and for builds using mingw-w64-x86_64-7.3.0-release-posix-seh error not reproduced.  
Thank you for fix, Oliver!
