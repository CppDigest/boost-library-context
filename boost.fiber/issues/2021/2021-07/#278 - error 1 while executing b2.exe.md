# #278 - error 1 while executing b2.exe [Closed]

> Username: gyorokpeter  
> Created at: 2021-07-12 08:00:50 UTC  
> Updated at: 2021-07-14 13:46:26 UTC  
> Closed at: 2021-07-13 18:29:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/278  

I'm trying to build [yuzu](https://github.com/yuzu-emu/yuzu/wiki/Building-For-Windows) using the instructions for MSYS2. I got up to this command:  
```cmake -G "MSYS Makefiles" ..```  
But this command seems to fail during the boost build.  
  
I have attached the full output of the command.  
[output.txt](https://github.com/boostorg/boost/files/6799594/output.txt)

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-07-12 18:01:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/278#issuecomment-878480844  

From the output I managed to see that b2 is invoked like this:  
```shell  
b2.exe -q numa=on target-os=windows architecture=x86 address-model=64 binary-format=pe abi=ms --layout=system --user-config=D:/.conan/0ec564/1/source_subfolder/tools/build/user-config.jam -sNO_ZLIB=0 -sNO_BZIP2=0 -sNO_LZMA=1 -sNO_ZSTD=1 boost.locale.icu=off --disable-icu boost.locale.iconv=off --disable-iconv threading=multi visibility=hidden link=static variant=release --with-atomic --with-chrono --with-container --with-context --with-contract --with-coroutine --with-date_time --with-exception --with-fiber --with-filesystem --with-graph --with-iostreams --with-locale --with-log --with-math --with-nowide --with-program_options --with-random --with-regex --with-serialization --with-stacktrace --with-system --with-test --with-thread --with-timer --with-type_erasure --with-wave toolset=gcc define=_GLIBCXX_USE_CXX11_ABI=1 pch=on  cxxflags="-fPIC" install --prefix=D:/.conan/58c6f8/1 -j16 --abbreviate-paths -d0 --debug-configuration --build-dir="D:/.conan/a86d98/1"  
```  
  
and `user-config.jam` is  
```jam  
using zlib : 1.2.11 : <include>"D:/Projects/.conan/data/zlib/1.2.11/_/_/package/c6817f477abe7e9a917b102f37dc1fd0c2d95f50/include" <search>"D:/Projects/.conan/data/zlib/1.2.11/_/_/package/c6817f477abe7e9a917b102f37dc1fd0c2d95f50/lib" <name>zlib ;  
using bzip2 : 1.0.8 : <include>"D:/Projects/.conan/data/bzip2/1.0.8/_/_/package/0766393ee44f07d2755696e76f200af2c3907715/include" <search>"D:/Projects/.conan/data/bzip2/1.0.8/_/_/package/0766393ee44f07d2755696e76f200af2c3907715/lib" <name>bz2 ;  
using "gcc" :  :  D:/msys64/mingw64/bin/g++.exe :  
 ;  
  
```   
  
The issue is that there's no information on what target it failed to build. Conan package for Boost has an option for log verbosity. Please open CMakeLists.txt for the project you're building, find where it sets `CONAN_LIB_OPTIONS` and add `boost:debug_level=2`.

---

## Comment 2

> Username: gyorokpeter  
> Created at: 2021-07-13 06:52:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/278#issuecomment-878827265  

Looks like this one:  
```  
libs\fiber\src\numa\windows\pin_thread.cpp:26:42: error: invalid conversion from 'HANDLE' {aka 'void*'} to 'std::thread::native_handle_type' {aka 'long long unsigned int'} [-fpermissive]  
   26 |     pin_thread( cpuid, ::GetCurrentThread() );  
      |                        ~~~~~~~~~~~~~~~~~~^~  
      |                                          |  
      |                                          HANDLE {aka void*}  
In file included from libs\fiber\src\numa\windows\pin_thread.cpp:7:  
./boost/fiber/numa/pin_thread.hpp:26:33: note:   initializing argument 2 of 'void boost::fibers::numa::pin_thread(uint32_t, std::thread::native_handle_type)'  
   26 | void pin_thread( std::uint32_t, std::thread::native_handle_type);  
      |                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ./boost/config.hpp:39,  
                 from ./boost/fiber/numa/pin_thread.hpp:13,  
                 from libs\fiber\src\numa\windows\pin_thread.cpp:7:  
libs\fiber\src\numa\windows\pin_thread.cpp: In function 'void boost::fibers::numa::pin_thread(uint32_t, std::thread::native_handle_type)':  
libs\fiber\src\numa\windows\pin_thread.cpp:40:57: error: invalid conversion from 'std::thread::native_handle_type' {aka 'long long unsigned int'} to 'HANDLE' {aka 'void*'} [-fpermissive]  
   40 |     if ( BOOST_UNLIKELY( 0 == ::SetThreadGroupAffinity( h, & affinity, nullptr) ) ) {  
      |                                                         ^  
      |                                                         |  
      |                                                         std::thread::native_handle_type {aka long long unsigned int}  
./boost/config/compiler/gcc.hpp:96:44: note: in definition of macro 'BOOST_UNLIKELY'  
   96 | #define BOOST_UNLIKELY(x) __builtin_expect(x, 0)  
      |                                            ^  
In file included from D:/msys64/mingw64/x86_64-w64-mingw32/include/winbase.h:30,  
                 from D:/msys64/mingw64/x86_64-w64-mingw32/include/windows.h:70,  
                 from libs\fiber\src\numa\windows\pin_thread.cpp:10:  
D:/msys64/mingw64/x86_64-w64-mingw32/include/processtopologyapi.h:22:60: note:   initializing argument 1 of 'WINBOOL SetThreadGroupAffinity(HANDLE, const GROUP_AFFINITY*, PGROUP_AFFINITY)'  
   22 |   WINBASEAPI WINBOOL WINAPI SetThreadGroupAffinity (HANDLE hThread, CONST GROUP_AFFINITY *GroupAffinity, PGROUP_AFFINITY PreviousGroupAffinity);  
      |                                                     ~~~~~~~^~~~~~~  
```  
[output.txt](https://github.com/boostorg/boost/files/6806516/output.txt)

---

## Comment 3

> Username: olk  
> Created at: 2021-07-13 18:26:46 UTC  
> Updated at: 2021-07-13 18:30:39 UTC  
> Url: https://github.com/boostorg/fiber/issues/278#issuecomment-879306313  

`std::thread::native_handle_type` == Returns the implementation defined underlying thread handle.   
MSDN: `std::thread::native_handle_type` is defined as a Win32 `HANDLE` that's cast as `void *` (https://docs.microsoft.com/en-us/cpp/standard-library/thread-class?view=msvc-160).   
  
But mingw64/STL implementation does  define `std::thread::native_handle_type` as `long long unsigned int` which is false as the Microsoft documentation states.  
  
So mingw64/your STL implementation has to be fixed.

---

## Comment 4

> Username: gyorokpeter  
> Created at: 2021-07-14 08:26:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/278#issuecomment-879700423  

Just to confirm, am I doing this right?  
Currently ```thread``` looks like this:  
```  
typedef __gthread_t native_handle_type;  
```  
And there are two different files where ```__gthread_t``` is defined: ```bits/gthr-default.h``` and ```gthr-posix.h```. In both this is defined as  
```  
typedef pthread_t __gthread_t;  
```  
```pthread_t``` is defined in ```pthread.h```:  
```  
typedef uintptr_t pthread_t  
```  
Should I change this to:  
```  
typedef void* pthread_t;  
```

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-07-14 09:22:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/278#issuecomment-879738402  

It's not something you need to change. It's something MinGW devs need to change. Alternatively, maybe yuzu devs know how to handle this issue.

---

## Comment 6

> Username: olk  
> Created at: 2021-07-14 13:46:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/278#issuecomment-879906770  

AFAIK mingw is a port of gcc to Windows. gcc has its origin on POSIX systems.  
Function SetThreadGroupAffinity() used in boost.fiber is a function of the Windows native API. It might be that mingw can not be used together with parts of the Windows native API because of some incompatibilities.
