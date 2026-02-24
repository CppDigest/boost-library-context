# #672 - try to build cmake+mingw+win10/64bit problem [Closed]

> Username: meir100  
> Created at: 2017-07-23 23:25:22 UTC  
> Updated at: 2017-08-16 00:05:58 UTC  
> Closed at: 2017-08-16 00:05:58 UTC  
> Url: https://github.com/boostorg/beast/issues/672  

build beast on win10/64 bit ENG + codeblocks + boost1.6.3(gcc itself) + openssl-1.1.0e  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-  
  
and the commands i used to do it are:  
cd C:\Beast-develop  
  
open cmake(gui version)  
C:\Beast-develop ---> C:\Beast-develop\bin64  
added boost1.6.3(gcc itself) path  
added openssl path  
configure  
  
cmake (gui) says that NO openssl exists.  
  
generate  
cd C:\Beast-develop\bin64  
C:\make-4.1\WinRel\gnumake > Beast.txt  
  
the text + image of the crash:  
  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
Boost version: 1.63.0  
Found the following Boost libraries:  
  system  
  coroutine  
  context  
  filesystem  
  program_options  
  thread  
  chrono  
  date_time  
  atomic  
Could NOT find OpenSSL, try to set the path to OpenSSL root folder in the system variable OPENSSL_ROOT_DIR (missing:  OPENSSL_SSL_LIBRARY OPENSSL_CRYPTO_LIBRARY OPENSSL_INCLUDE_DIR)   
OpenSSL not found.  
Configuring done  
  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
[  1%] Building CXX object test/CMakeFiles/lib-tests.dir/__/extras/beast/unit_test/main.cpp.obj  
test\CMakeFiles\lib-tests.dir\build.make:62: recipe for target 'test/CMakeFiles/lib-tests.dir/__/extras/beast/unit_test/main.cpp.obj' failed  
CMakeFiles\Makefile2:107: recipe for target 'test/CMakeFiles/lib-tests.dir/all' failed  
Makefile:82: recipe for target 'all' failed  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
![beans_crash_1](https://user-images.githubusercontent.com/19237619/28503908-44a13c54-7017-11e7-9443-780a28638972.jpg)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-23 23:50:25 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-317291164  

No idea to be honest. Have you tried setting OPENSSL_ROOT_DIR?

---

## Comment 2

> Username: meir100  
> Created at: 2017-07-24 02:13:55 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-317302686  

how cam i define OPENSSL_ROOT_DIR from within cmake(gui), and if there is no way how can i do it from outside so cmake can use it when i activate the cmake(gui)?  
  
for ex:  
cmake -DOPENSSL_ROOT_DIR=/usr/local/ssl -DOPENSSL_LIBRARIES=/usr/local/ssl/lib  
cmake -DOPENSSL_ROOT_DIR=C:\openssl-1.1.0e -DOPENSSL_LIBRARIES=C:\openssl-1.1.0e\lib

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-24 02:50:48 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-317306628  

I don't know how MinGW works, its not a supported platform. I see you're compiling with gcc on windows, that is completely untested so I can't make any assurances that it will work.

---

## Comment 4

> Username: xsacha  
> Created at: 2017-07-26 12:00:00 UTC  
> Updated at: 2017-07-26 12:03:31 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-318032480  

Is it only the tests failing?  
It looks like it is trying OutputDebugStringW, which is only valid on MSVC. There is probably checks for BOOST_OS_WINDOWS instead of _MSC_VER  
To support mingw you basically just treat it like GCC. Instead of checking for Windows to do windows-specific tasks, you check for msvc.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-26 13:53:11 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-318059516  

If either of you want to submit a pull request, I can merge it. But I have no way of testing MinGW so I can't do it myself.

---

## Comment 6

> Username: Broekman  
> Created at: 2017-07-26 23:46:41 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-318215334  

The CMake works perfectly with MinGW-W64 i.c.w. GCC under Windows 10. Try using MSYS2 (http://www.msys2.org/) and install MinGW-W64. This also has (more) WinAPI support.  
  
Follow the installation steps on the site and after that install the dependencies in an MYSYS2 shell (C:\msys64\msys2.exe) or MinGW-w64 shell (C:\msys64\mingw64.exe):  
1. pacman -S mingw-w64-x86_64-gcc  
2. pacman -S mingw-w64-x86_64-make  
3. pacman -S mingw-w64-x86_64-boost  
4. pacman -S mingw-w64-x86_64-cmake  
5. pacman -S mingw-w64-x86_64-openssl  
Finally add "C:\msys64\mingw64\bin" (or wherever you installed it) to your environment path or manually link the libraries.  
  
Build in a MinGW-w64 shell (C:\msys64\mingw64.exe):  
1. mkdir build && cd build  
2. cmake .. -DCMAKE_CXX_COMPILER=C:/msys64/mingw64/bin/g++.exe -DCMAKE_C_COMPILER=C:/msys64/mingw64/bin/gcc.exe -G "MinGW Makefiles"  
3. mingw32-make.exe  
  
Building will fail since some of the WinAPI stuff used by beast in the unit tests (debugAPI) is missing in both MinGW and MinGW-W64. Most core functionality however should work just fine in your own projects. An example CMakeLists.txt should contain at least the following:  
1. find_package(Boost REQUIRED COMPONENTS system)  
2. find_package(OpenSSL REQUIRED)  
3. include_directories (SYSTEM ${Boost_INCLUDE_DIRS})  
4. set(WINSOCK_LINK_LIBRARIES_FLAGS "-lws2_32 -lmswsock")  
5. target_link_libraries(your_executable  
        ${Boost_LIBRARIES}  
        ${OPENSSL_LIBRARIES}  
        ${WINSOCK_LINK_LIBRARIES_FLAGS})

---

## Comment 7

> Username: Broekman  
> Created at: 2017-07-27 00:55:46 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-318225620  

To build the examples with MinGW-W64 (follow up of my previous [comment](https://github.com/boostorg/beast/issues/672#issuecomment-318215334)):  
  
Replace the following on line 76 in the CMakeLists.txt:  
```  
if (MINGW)  
    link_libraries(ws2_32 mswsock)  
endif()  
```  
With:  
```  
if (MINGW)  
    if (Beast_BUILD_TESTS)  
        message(FATAL_ERROR "Tests NOT supported under MinGW. Rerun CMake with: -DBeast_BUILD_TESTS=OFF")  
    endif()  
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wa,-mbig-obj -O2")  
    find_package(Boost REQUIRED COMPONENTS system filesystem program_options) #Hotfix for some missing links.  
    link_libraries(ws2_32 mswsock ${Boost_LIBRARIES})  
endif()  
```  
  
Build with:  
```  
mkdir build && cd build  
cmake .. -DCMAKE_CXX_COMPILER=C:/msys64/mingw64/bin/g++.exe -DCMAKE_C_COMPILER=C:/msys64/mingw64/bin/gcc.exe -G "MinGW Makefiles" -DBeast_BUILD_TESTS=OFF  
mingw32-make.exe  
```  
  
For some reason it wouldn't finish linking without specifying -O2 (or O3). Produces error:  
```  
C:/msys64/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/7.1.0/../../../../x86_64-w64-mingw32/bin/as.exe: CMakeFiles\server-framework.dir\main.cpp.obj: section .text$_ZN5boost4asio6detail17async_result_initINS1_17rewrapped_handlerINS_5beast9websocket6streamINS0_19basic_stream_socketINS0_2ip3tcpENS0_21stream_socket_serviceIS9_EEEEE7fail_opINSD_7read_opINS4_18basic_multi_bufferISaIcEEENS1_15wrapped_handlerINS0_10io_service6strandESt5_BindIFMN9framework17async_ws_con_baseINSN_12async_ws_conEEEFvNS_6system10error_codeEESt10shared_ptrISP_ESt12_PlaceholderILi1EEEENS1_26is_continuation_if_runningEEEEEEES10_EEFvvEEC1EOS15_: string table overflow at offset 10000336  
C:\msys64\tmp\ccFPWGt0.s: Assembler messages:  
C:\msys64\tmp\ccFPWGt0.s: Fatal error: can't close CMakeFiles\server-framework.dir\main.cpp.obj: File too big  
mingw32-make[2]: *** [example\server-framework\CMakeFiles\server-framework.dir\build.make:63: example/server-framework/CMakeFiles/server-framework.dir/main.cpp.obj] Error 1  
mingw32-make[1]: *** [CMakeFiles\Makefile2:463: example/server-framework/CMakeFiles/server-framework.dir/all] Error 2  
mingw32-make: *** [Makefile:83: all] Error 2  
```

---

## Comment 8

> Username: meir100  
> Created at: 2017-07-28 18:54:28 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-318735043  

there are a few tools, grid managers that work with MSYS2, but this is exactly the problem, i want BEAST to build itself on: win10-64/gcc/codeblocks/boost(gcc version)/cmake without the 6/7/8/9 steps or install another package like: MSYS2.  
  
question: if BOOST itself can be build with: win10-64/gcc/codeblocks/boost(gcc version)/cmake and BEAST is built with BOOST inside, then why it cannot be build automaticaly, what is preventing?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-07-28 18:58:30 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-318735916  

>then why it cannot be build automaticaly, what is preventing?  
  
I would like it to work for you if it can be fixed, but I would like to point this out:  
  
Beast versions 86 and later do not use `find_package`. Instead, it is required that Beast is located inside the boost tree itself. For example you have to put Beast at `$BOOST_ROOT/libs/beast`, in order for beast's CMakeLists.txt to work. And it will only work on Windows since there is no easy way for me to figure out the names of the boost libraries to be linked with.  
  
This situation is unfortunate but a consequence of Beast becoming part of Boost. I think the trade-off is worth it but there are some casualties such as this use-case.  
  
@Broekman What version of Beast are you using?  
  
@meir100 What version of Beast are you using?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-07-28 19:04:31 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-318737277  

>Could NOT find OpenSSL, try to set the path to OpenSSL root folder in the system variable OPENSSL_ROOT_DIR (missing: OPENSSL_SSL_LIBRARY OPENSSL_CRYPTO_LIBRARY OPENSSL_INCLUDE_DIR)  
OpenSSL not found.  
  
If you install OpenSSL using the Windows installer, it will set a Windows environment variable called `OPENSSL_CONF` and some registry entries which CMake's **find_package** can use. Beast's CMakeLists.txt should automatically find OpenSSL (32 or 64 bit) if you have used the Windows installer.

---

## Comment 11

> Username: Broekman  
> Created at: 2017-07-28 19:08:40 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-318738142  

@vinniefalco Did the steps above with version 86.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-08-05 15:37:53 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-320450415  

Is this still an issue that requires attention?

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-08-16 00:05:58 UTC  
> Url: https://github.com/boostorg/beast/issues/672#issuecomment-322622149  

It looks like this has either been resolved, or there is nothing actionable so I will go ahead and close the issue. If you still think there's a problem please feel free to re-open it or better yet, create a new issue! Thanks!
