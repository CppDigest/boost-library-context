# #901 - try to build beast with boost 1.66.0 + gcc + codeblocks + win10-pro-64bit [Closed]

> Username: meir100  
> Created at: 2017-11-23 10:19:20 UTC  
> Updated at: 2018-04-24 22:25:59 UTC  
> Closed at: 2018-04-24 22:25:58 UTC  
> Url: https://github.com/boostorg/beast/issues/901  

try to build beast with boost 1.66.0 + gcc + codeblocks + win10-pro-64bit after using cmake + openssl.  
output for: codeblocks+mingw.  
cmake created codeblocks project.  
and Build messages:  
  
||=== Build: all in Beast (compiler: GNU GCC Compiler) ===|  
C:\beast-master\include\boost\beast\core\detail\config.hpp|13|fatal error: boost/config.hpp: No such file or directory|  
example\advanced\server\CMakeFiles\advanced-server.dir\build.make|62|recipe for target 'example/advanced/server/CMakeFiles/advanced-server.dir/advanced_server.cpp.obj' failed|  
CMakeFiles\Makefile2|132|recipe for target 'example/advanced/server/CMakeFiles/advanced-server.dir/all' failed|  
C:\beast-master\build\Makefile|82|recipe for target 'all' failed|  
||=== Build failed: 4 error(s), 0 warning(s) (0 minute(s), 7 second(s)) ===|

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-23 10:23:53 UTC  
> Url: https://github.com/boostorg/beast/issues/901#issuecomment-346579882  

I have no idea what you are doing here. Are you using the CMakeLists.txt that comes with Beast?

---

## Comment 2

> Username: meir100  
> Created at: 2017-11-23 23:10:08 UTC  
> Url: https://github.com/boostorg/beast/issues/901#issuecomment-346707782  

can you write the command you use to activate cmake from the (DOS) command line that uses the file CMakeLists.txt?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-11-24 02:39:50 UTC  
> Url: https://github.com/boostorg/beast/issues/901#issuecomment-346728696  

From the local beast directory (`$BOOST_DIR/libs/beast`):  
```  
mkdir bin  
cd bin  
cmake ..  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-11-24 02:40:52 UTC  
> Url: https://github.com/boostorg/beast/issues/901#issuecomment-346728841  

![image](https://user-images.githubusercontent.com/1503976/33193488-d3d9c0c8-d07d-11e7-9f48-1da0389902d2.png)

---

## Comment 5

> Username: meir100  
> Created at: 2017-11-25 01:19:19 UTC  
> Url: https://github.com/boostorg/beast/issues/901#issuecomment-346911122  

when i use this commands cmake is using vs2017, but this is the environment i want to avoid and use "CodeBlocks + MinGW" in order to make it transparent to linux.  
  
for boost (64 bit win10/pro):  
bootstrap.bat gcc  
b2.exe toolset=gcc link=shared  
  
for beast:  
  
CMake GUI  
  
C:\boost_1_66_0_GCC\libs\beast   
C:/boost_1_66_0_GCC/libs/beast/bin  
  
define ssl path: C:\openssl-1.1.0e  
  
Configure:  CodeBlocks + MinGW  Makefiles  
  
get the message:  
  
Could NOT find OpenSSL, try to set the path to OpenSSL root folder in the system variable OPENSSL_ROOT_DIR (missing:  OPENSSL_SSL_LIBRARY OPENSSL_CRYPTO_LIBRARY) (found version "1.1.0e")  
OpenSSL not found.  
Configuring done  
  
(defined OPENSSL_SSL_LIBRARY + OPENSSL_CRYPTO_LIBRARY in windows 10 variables, but get this message)  
  
from: C:/boost_1_66_0_GCC/libs/beast/bin  
cmake C:\boost_1_66_0_GCC\libs\beast   
  
CodeBlocks project and build files created   
  
doubleclick: CodeBlocks project  
Build  
  
||=== Build: all in Beast (compiler: GNU GCC Compiler) ===|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZN5boost6system10error_codeC1Ev[_ZN5boost6system10error_codeC1Ev]+0x17)||undefined reference to `boost::system::system_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZN5boost6system4errc20make_error_conditionENS1_6errc_tE[_ZN5boost6system4errc20make_error_conditionENS1_6errc_tE]+0x10)||undefined reference to `boost::system::generic_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZNK5boost6system14error_category12std_category10equivalentEiRKSt15error_condition[_ZNK5boost6system14error_category12std_category10equivalentEiRKSt15error_condition]+0xa9)||undefined reference to `boost::system::generic_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZNK5boost6system14error_category12std_category10equivalentEiRKSt15error_condition[_ZNK5boost6system14error_category12std_category10equivalentEiRKSt15error_condition]+0xe4)||undefined reference to `boost::system::generic_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZNK5boost6system14error_category12std_category10equivalentERKSt10error_codei[_ZNK5boost6system14error_category12std_category10equivalentERKSt10error_codei]+0xab)||undefined reference to `boost::system::generic_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZNK5boost6system14error_category12std_category10equivalentERKSt10error_codei[_ZNK5boost6system14error_category12std_category10equivalentERKSt10error_codei]+0xe6)||undefined reference to `boost::system::generic_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZN5boost4asio5error19get_system_categoryEv[_ZN5boost4asio5error19get_system_categoryEv]+0x9)||undefined reference to `boost::system::system_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZN5boost5beast10file_stdio5closeERNS_6system10error_codeE[_ZN5boost5beast10file_stdio5closeERNS_6system10error_codeE]+0x46)||undefined reference to `boost::system::generic_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZN5boost5beast10file_stdio4openEPKcNS0_9file_modeERNS_6system10error_codeE[_ZN5boost5beast10file_stdio4openEPKcNS0_9file_modeERNS_6system10error_codeE]+0xfc)||undefined reference to `boost::system::generic_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZNK5boost5beast10file_stdio4sizeERNS_6system10error_codeE[_ZNK5boost5beast10file_stdio4sizeERNS_6system10error_codeE]+0x23)||undefined reference to `boost::system::generic_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZNK5boost5beast10file_stdio4sizeERNS_6system10error_codeE[_ZNK5boost5beast10file_stdio4sizeERNS_6system10error_codeE]+0x61)||undefined reference to `boost::system::generic_category()'|  
  
CMakeFiles\advanced-server.dir\objects.a(advanced_server.cpp.obj)advanced_server.cpp:(.text$_ZNK5boost5beast10file_stdio4sizeERNS_6system10error_codeE[_ZNK5boost5beast10file_stdio4sizeERNS_6system10error_codeE]+0xb0)||undefined reference to `boost::system::generic_category()'|  
||error: ld returned 1 exit status|  
example\advanced\server\CMakeFiles\advanced-server.dir\build.make|96|recipe for target 'example/advanced/server/advanced-server.exe' failed|  
  
CMakeFiles\Makefile2|132|recipe for target 'example/advanced/server/CMakeFiles/advanced-server.dir/all' failed|  
  
C:\boost_1_66_0_GCC\libs\beast\bin\Makefile|82|recipe for target 'all' failed|  
  
||=== Build failed: 16 error(s), 0 warning(s) (3 minute(s), 6 second(s)) ===|  
  
  
but inside: CodeBlocks > Settings > Compiler > Linker Settings:  
added:   
C:\boost_1_66_0_GCC\stage\lib libboost*system* libraries (all this system libraries)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-11-25 02:55:37 UTC  
> Url: https://github.com/boostorg/beast/issues/901#issuecomment-346914801  

Beast is header-only. To use Beast in your own projects, you do not need the CMakeLists.txt or the Jamfile that comes with Beast. You only need to configure your compiler or build system to recognize the beast include directory, and to link with the Boost.System library (and optionally OpenSSL or Boost.Coroutine).  
  
If you want to build the examples and tests that come with Beast, you have three choices:  
  
1. Use bjam/b2 and the Jamfiles that come with Beast, with a correct configuration for your build environment (user-config.jam). If you have trouble with Boost.Build you can ask for help on the Boost Users mailing list (see https://boost.org).  
  
2. Use the CMakeLists.txt that come with Beast, with Windows and Visual Studio.  
  
3. Use your own build scripts.  
  
Only 1 and 2 are officially supported. For 3 you will have to get them working yourself.  
  
Does this answer all your questions?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-12-02 14:38:02 UTC  
> Url: https://github.com/boostorg/beast/issues/901#issuecomment-348696131  

Has this issue been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:39 UTC  
> Url: https://github.com/boostorg/beast/issues/901#issuecomment-384003592  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-04-24 22:25:58 UTC  
> Url: https://github.com/boostorg/beast/issues/901#issuecomment-384099537  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
