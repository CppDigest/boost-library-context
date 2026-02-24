# #385 - CMake link of a non-existent file failed in VS 2022 [Closed]

> Username: jppgmx  
> Created at: 2023-04-15 13:20:00 UTC  
> Updated at: 2023-04-23 15:36:38 UTC  
> Closed at: 2023-04-23 15:27:27 UTC  
> Url: https://github.com/boostorg/thread/issues/385  

### Introduction  
  
I'm using this module to create some thread-safe classes using `mutex` and `locks`. Since I don't use all of the boost libraries, I run this command:  
```  
./b2 --build-type=minimal --with-thread --with-atomic variant=debug threading=multi link=shared runtime-link=shared  
```  
The result is these files:  
```  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----        15/04/2023     09:56                cmake  
-a----        15/04/2023     09:56          25600 boost_atomic-vc143-mt-gd-x32-1_78.dll  
-a----        15/04/2023     09:56           7786 boost_atomic-vc143-mt-gd-x32-1_78.lib  
-a----        15/04/2023     09:56         700416 boost_atomic-vc143-mt-gd-x32-1_78.pdb  
-a----        15/04/2023     09:56          36864 boost_atomic-vc143-mt-gd-x64-1_78.dll  
-a----        15/04/2023     09:56           7886 boost_atomic-vc143-mt-gd-x64-1_78.lib  
-a----        15/04/2023     09:56         724992 boost_atomic-vc143-mt-gd-x64-1_78.pdb  
-a----        15/04/2023     09:56          55808 boost_chrono-vc143-mt-gd-x32-1_78.dll  
-a----        15/04/2023     09:56          24826 boost_chrono-vc143-mt-gd-x32-1_78.lib  
-a----        15/04/2023     09:56        1388544 boost_chrono-vc143-mt-gd-x32-1_78.pdb  
-a----        15/04/2023     09:56          64000 boost_chrono-vc143-mt-gd-x64-1_78.dll  
-a----        15/04/2023     09:56          25090 boost_chrono-vc143-mt-gd-x64-1_78.lib  
-a----        15/04/2023     09:56        1413120 boost_chrono-vc143-mt-gd-x64-1_78.pdb  
-a----        15/04/2023     09:56         148992 boost_thread-vc143-mt-gd-x32-1_78.dll  
-a----        15/04/2023     09:56          29048 boost_thread-vc143-mt-gd-x32-1_78.lib  
-a----        15/04/2023     09:56        2641920 boost_thread-vc143-mt-gd-x32-1_78.pdb  
-a----        15/04/2023     09:56         176640 boost_thread-vc143-mt-gd-x64-1_78.dll  
-a----        15/04/2023     09:56          29454 boost_thread-vc143-mt-gd-x64-1_78.lib  
-a----        15/04/2023     09:56        2699264 boost_thread-vc143-mt-gd-x64-1_78.pdb  
```  
I use CMake in my project, so I use these instructions:  
```cmake  
if(PROJECT_DEBUG)  
    set(Boost_DEBUG ON)  
    set(Boost_USE_DEBUG_LIBS ON)  
    set(Boost_USE_DEBUG_RUNTIME  ON )  
else()  
    set(Boost_USE_RELEASE_LIBS ON)   
    set(Boost_USE_DEBUG_RUNTIME OFF)  
endif()  
  
set(Boost_USE_STATIC_LIBS    OFF)  #EDIT: was OFF, not ON  
set(Boost_USE_MULTITHREADED  ON)    
set(Boost_USE_STATIC_RUNTIME OFF)   
  
find_package(Boost 1.78 REQUIRED COMPONENTS thread)   
  
include_directories(${Boost_INCLUDE_DIRS})  
link_directories(${Boost_LIBRARIES})  
```  
### Problem  
The project configuration is successful, however, in a very strange way, the `LINK` complains that the following file `libboost_thread-vc143-mt-gd-x64-1_78.lib` could not be opened. Even if there is no such file the problem persists, how can I solve it? Need a compiler definition? Change `find_package` options? Or the command of `b2`?  
### Extra Information  
- OS: Windows 11;  
- Arch: x64;  
- I'm using Visual Studio 2022;  
   - With Windows 10 SDK;  
   - With CMake 3.25.1-msvc1  
   - With MSVC toolset v143;  
- Boost 1.78  
- The files of Boost are located in `C:\boost_1_78`

---

## Comment 1

> Username: Kojoley  
> Created at: 2023-04-22 22:23:01 UTC  
> Url: https://github.com/boostorg/thread/issues/385#issuecomment-1518886921  

> `libboost_thread-vc143-mt-gd-x64-1_78.lib`  
  
`lib` name prefix is used for mingw, either there is a bug in cmake scripts or for some reason cmake is configuring for mingw instead of msvc but you did not include cmake configuration log would help to understand what went wrong.

---

## Comment 2

> Username: jppgmx  
> Created at: 2023-04-23 13:07:57 UTC  
> Updated at: 2023-04-23 13:14:06 UTC  
> Url: https://github.com/boostorg/thread/issues/385#issuecomment-1519064167  

### Update  
I switched to version 1.79 and downloaded the binaries from SourceForge and the problem still persists.  
  
Below is the configuration log _(I omitted the path of my user folder):_  
```  
1> [CMake] -- Selecting Windows SDK version 10.0.20348.0 to target Windows 10.0.22621.  
1> [CMake] -- The CXX compiler identification is MSVC 19.35.32215.0  
1> [CMake] -- Detecting CXX compiler ABI info  
1> [CMake] -- Detecting CXX compiler ABI info - done  
1> [CMake] -- Check for working CXX compiler: C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.35.32215/bin/Hostx64/x64/cl.exe - skipped  
1> [CMake] -- Detecting CXX compile features  
1> [CMake] -- Detecting CXX compile features - done  
1> [CMake] -- Found JNI: C:/Program Files/Eclipse Adoptium/jdk-17.0.6.10-hotspot/include (Required is at least version "17") found components: AWT JVM   
1> [CMake] -- Found Python: C:/Program Files/WindowsApps/PythonSoftwareFoundation.Python.3.10_3.10.2800.0_x64__qbz5n2kfra8p0/python3.10.exe (found suitable version "3.10.10", minimum required is "3.8") found components: Interpreter   
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1689 ] _boost_TEST_VERSIONS = "1.80.0;1.80;1.79.0;1.79"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1690 ] Boost_USE_MULTITHREADED = "ON"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1691 ] Boost_USE_STATIC_LIBS = "OFF"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1692 ] Boost_USE_STATIC_RUNTIME = "OFF"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1693 ] Boost_ADDITIONAL_VERSIONS = <unset>  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1694 ] Boost_NO_SYSTEM_PATHS = <unset>  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1726 ] BOOST_ROOT = "C:\boost_1_79_0"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1727 ] ENV{BOOST_ROOT} = <unset>  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1728 ] BOOST_INCLUDEDIR = <unset>  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1729 ] ENV{BOOST_INCLUDEDIR} = <unset>  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1730 ] BOOST_LIBRARYDIR = <unset>  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1731 ] ENV{BOOST_LIBRARYDIR} = <unset>  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1803 ] _boost_INCLUDE_SEARCH_DIRS = "C:\boost_1_79_0/include;C:\boost_1_79_0;PATHS;C:/local/boost_1_80_0;PATHS;C:/local/boost_1_80;PATHS;C:/local/boost_1_79_0;PATHS;C:/local/boost_1_79;PATHS;C:/boost/include;C:/boost;/sw/local/include"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1804 ] _boost_PATH_SUFFIXES = "boost-1_80_0;boost_1_80_0;boost/boost-1_80_0;boost/boost_1_80_0;boost-1_80;boost_1_80;boost/boost-1_80;boost/boost_1_80;boost-1_79_0;boost_1_79_0;boost/boost-1_79_0;boost/boost_1_79_0;boost-1_79;boost_1_79;boost/boost-1_79;boost/boost_1_79"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1819 ] location of version.hpp: C:/boost_1_79_0/boost/version.hpp  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1859 ] Boost_VERSION = "107900"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1860 ] Boost_VERSION_STRING = "1.79.0"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1861 ] Boost_VERSION_MACRO = "107900"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1862 ] Boost_VERSION_MAJOR = "1"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1863 ] Boost_VERSION_MINOR = "79"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1864 ] Boost_VERSION_PATCH = "0"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1865 ] Boost_VERSION_COUNT = "3"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1889 ] Boost_LIB_PREFIX = ""  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1890 ] Boost_NAMESPACE = "boost"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:953 ] _boost_COMPILER = "-vc143;-vc142;-vc141;-vc140" (guessed)  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:1926 ] _boost_MULTITHREADED = "-mt"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2004 ] _boost_ARCHITECTURE_TAG = "-x64" (detected)  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2008 ] _boost_RELEASE_ABI_TAG = "-"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2009 ] _boost_DEBUG_ABI_TAG = "-gd"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2069 ] _boost_LIBRARY_SEARCH_DIRS_RELEASE = "C:\boost_1_79_0/lib;C:\boost_1_79_0/stage/lib;C:\boost_1_79_0/lib64-msvc-14.3;C:\boost_1_79_0/lib64-msvc-14.2;C:\boost_1_79_0/lib64-msvc-14.1;C:\boost_1_79_0/lib64-msvc-14.0;C:/boost_1_79_0/lib;C:/boost_1_79_0/../lib;C:/boost_1_79_0/stage/lib;C:/boost_1_79_0/../lib64-msvc-14.3;C:/boost_1_79_0/../lib64-msvc-14.2;C:/boost_1_79_0/../lib64-msvc-14.1;C:/boost_1_79_0/../lib64-msvc-14.0;C:/boost_1_79_0/lib64-msvc-14.3;C:/boost_1_79_0/lib64-msvc-14.2;C:/boost_1_79_0/lib64-msvc-14.1;C:/boost_1_79_0/lib64-msvc-14.0;C:/local/boost_1_80_0/lib64-msvc-14.3;C:/local/boost_1_80_0/lib64-msvc-14.2;C:/local/boost_1_80_0/lib64-msvc-14.1;C:/local/boost_1_80_0/lib64-msvc-14.0;C:/local/boost_1_80/lib64-msvc-14.3;C:/local/boost_1_80/lib64-msvc-14.2;C:/local/boost_1_80/lib64-msvc-14.1;C:/local/boost_1_80/lib64-msvc-14.0;C:/local/boost_1_79_0/lib64-msvc-14.3;C:/local/boost_1_79_0/lib64-msvc-14.2;C:/local/boost_1_79_0/lib64-msvc-14.1;C:/local/boost_1_79_0/lib64-msvc-14.0;C:/local/boost_1_79/lib64-msvc-14.3;C:/local/boost_1_79/lib64-msvc-14.2;C:/local/boost_1_79/lib64-msvc-14.1;C:/local/boost_1_79/lib64-msvc-14.0;C:/boost/lib64-msvc-14.3;C:/boost/lib64-msvc-14.2;C:/boost/lib64-msvc-14.1;C:/boost/lib64-msvc-14.0;PATHS;C:/boost/lib;C:/boost;/sw/local/lib"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2070 ] _boost_LIBRARY_SEARCH_DIRS_DEBUG = "C:\boost_1_79_0/lib;C:\boost_1_79_0/stage/lib;C:\boost_1_79_0/lib64-msvc-14.3;C:\boost_1_79_0/lib64-msvc-14.2;C:\boost_1_79_0/lib64-msvc-14.1;C:\boost_1_79_0/lib64-msvc-14.0;C:/boost_1_79_0/lib;C:/boost_1_79_0/../lib;C:/boost_1_79_0/stage/lib;C:/boost_1_79_0/../lib64-msvc-14.3;C:/boost_1_79_0/../lib64-msvc-14.2;C:/boost_1_79_0/../lib64-msvc-14.1;C:/boost_1_79_0/../lib64-msvc-14.0;C:/boost_1_79_0/lib64-msvc-14.3;C:/boost_1_79_0/lib64-msvc-14.2;C:/boost_1_79_0/lib64-msvc-14.1;C:/boost_1_79_0/lib64-msvc-14.0;C:/local/boost_1_80_0/lib64-msvc-14.3;C:/local/boost_1_80_0/lib64-msvc-14.2;C:/local/boost_1_80_0/lib64-msvc-14.1;C:/local/boost_1_80_0/lib64-msvc-14.0;C:/local/boost_1_80/lib64-msvc-14.3;C:/local/boost_1_80/lib64-msvc-14.2;C:/local/boost_1_80/lib64-msvc-14.1;C:/local/boost_1_80/lib64-msvc-14.0;C:/local/boost_1_79_0/lib64-msvc-14.3;C:/local/boost_1_79_0/lib64-msvc-14.2;C:/local/boost_1_79_0/lib64-msvc-14.1;C:/local/boost_1_79_0/lib64-msvc-14.0;C:/local/boost_1_79/lib64-msvc-14.3;C:/local/boost_1_79/lib64-msvc-14.2;C:/local/boost_1_79/lib64-msvc-14.1;C:/local/boost_1_79/lib64-msvc-14.0;C:/boost/lib64-msvc-14.3;C:/boost/lib64-msvc-14.2;C:/boost/lib64-msvc-14.1;C:/boost/lib64-msvc-14.0;PATHS;C:/boost/lib;C:/boost;/sw/local/lib"  
1> [CMake] -- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
1> [CMake] -- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed  
1> [CMake] -- Looking for pthread_create in pthreads  
1> [CMake] -- Looking for pthread_create in pthreads - not found  
1> [CMake] -- Looking for pthread_create in pthread  
1> [CMake] -- Looking for pthread_create in pthread - not found  
1> [CMake] -- Found Threads: TRUE    
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2255 ] Searching for THREAD_LIBRARY_RELEASE: boost_thread-vc143-mt-x64-1_79;boost_thread-vc143-mt-x64;boost_thread-vc143-mt;boost_thread-vc142-mt-x64-1_79;boost_thread-vc142-mt-x64;boost_thread-vc142-mt;boost_thread-vc141-mt-x64-1_79;boost_thread-vc141-mt-x64;boost_thread-vc141-mt;boost_thread-vc140-mt-x64-1_79;boost_thread-vc140-mt-x64;boost_thread-vc140-mt;boost_thread-mt-x64-1_79;boost_thread-mt-x64;boost_thread-mt;boost_thread-mt;boost_thread  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2310 ] Searching for THREAD_LIBRARY_DEBUG: boost_thread-vc143-mt-gd-x64-1_79;boost_thread-vc143-mt-gd-x64;boost_thread-vc143-mt-gd;boost_thread-vc142-mt-gd-x64-1_79;boost_thread-vc142-mt-gd-x64;boost_thread-vc142-mt-gd;boost_thread-vc141-mt-gd-x64-1_79;boost_thread-vc141-mt-gd-x64;boost_thread-vc141-mt-gd;boost_thread-vc140-mt-gd-x64-1_79;boost_thread-vc140-mt-gd-x64;boost_thread-vc140-mt-gd;boost_thread-mt-gd-x64-1_79;boost_thread-mt-gd-x64;boost_thread-mt-gd;boost_thread-mt;boost_thread  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:807 ] Boost_LIBRARY_DIR_DEBUG = "C:/boost_1_79_0/lib64-msvc-14.3"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:809 ] _boost_LIBRARY_SEARCH_DIRS_DEBUG = "C:/boost_1_79_0/lib64-msvc-14.3;NO_DEFAULT_PATH;NO_CMAKE_FIND_ROOT_PATH"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2255 ] Searching for SYSTEM_LIBRARY_RELEASE: boost_system-vc143-mt-x64-1_79;boost_system-vc143-mt-x64;boost_system-vc143-mt;boost_system-vc142-mt-x64-1_79;boost_system-vc142-mt-x64;boost_system-vc142-mt;boost_system-vc141-mt-x64-1_79;boost_system-vc141-mt-x64;boost_system-vc141-mt;boost_system-vc140-mt-x64-1_79;boost_system-vc140-mt-x64;boost_system-vc140-mt;boost_system-mt-x64-1_79;boost_system-mt-x64;boost_system-mt;boost_system-mt;boost_system  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2310 ] Searching for SYSTEM_LIBRARY_DEBUG: boost_system-vc143-mt-gd-x64-1_79;boost_system-vc143-mt-gd-x64;boost_system-vc143-mt-gd;boost_system-vc142-mt-gd-x64-1_79;boost_system-vc142-mt-gd-x64;boost_system-vc142-mt-gd;boost_system-vc141-mt-gd-x64-1_79;boost_system-vc141-mt-gd-x64;boost_system-vc141-mt-gd;boost_system-vc140-mt-gd-x64-1_79;boost_system-vc140-mt-gd-x64;boost_system-vc140-mt-gd;boost_system-mt-gd-x64-1_79;boost_system-mt-gd-x64;boost_system-mt-gd;boost_system-mt;boost_system  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:807 ] Boost_LIBRARY_DIR_DEBUG = "C:/boost_1_79_0/lib64-msvc-14.3"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:809 ] _boost_LIBRARY_SEARCH_DIRS_DEBUG = "C:/boost_1_79_0/lib64-msvc-14.3;NO_DEFAULT_PATH;NO_CMAKE_FIND_ROOT_PATH"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2255 ] Searching for CHRONO_LIBRARY_RELEASE: boost_chrono-vc143-mt-x64-1_79;boost_chrono-vc143-mt-x64;boost_chrono-vc143-mt;boost_chrono-vc142-mt-x64-1_79;boost_chrono-vc142-mt-x64;boost_chrono-vc142-mt;boost_chrono-vc141-mt-x64-1_79;boost_chrono-vc141-mt-x64;boost_chrono-vc141-mt;boost_chrono-vc140-mt-x64-1_79;boost_chrono-vc140-mt-x64;boost_chrono-vc140-mt;boost_chrono-mt-x64-1_79;boost_chrono-mt-x64;boost_chrono-mt;boost_chrono-mt;boost_chrono  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2310 ] Searching for CHRONO_LIBRARY_DEBUG: boost_chrono-vc143-mt-gd-x64-1_79;boost_chrono-vc143-mt-gd-x64;boost_chrono-vc143-mt-gd;boost_chrono-vc142-mt-gd-x64-1_79;boost_chrono-vc142-mt-gd-x64;boost_chrono-vc142-mt-gd;boost_chrono-vc141-mt-gd-x64-1_79;boost_chrono-vc141-mt-gd-x64;boost_chrono-vc141-mt-gd;boost_chrono-vc140-mt-gd-x64-1_79;boost_chrono-vc140-mt-gd-x64;boost_chrono-vc140-mt-gd;boost_chrono-mt-gd-x64-1_79;boost_chrono-mt-gd-x64;boost_chrono-mt-gd;boost_chrono-mt;boost_chrono  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:807 ] Boost_LIBRARY_DIR_DEBUG = "C:/boost_1_79_0/lib64-msvc-14.3"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:809 ] _boost_LIBRARY_SEARCH_DIRS_DEBUG = "C:/boost_1_79_0/lib64-msvc-14.3;NO_DEFAULT_PATH;NO_CMAKE_FIND_ROOT_PATH"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2255 ] Searching for ATOMIC_LIBRARY_RELEASE: boost_atomic-vc143-mt-x64-1_79;boost_atomic-vc143-mt-x64;boost_atomic-vc143-mt;boost_atomic-vc142-mt-x64-1_79;boost_atomic-vc142-mt-x64;boost_atomic-vc142-mt;boost_atomic-vc141-mt-x64-1_79;boost_atomic-vc141-mt-x64;boost_atomic-vc141-mt;boost_atomic-vc140-mt-x64-1_79;boost_atomic-vc140-mt-x64;boost_atomic-vc140-mt;boost_atomic-mt-x64-1_79;boost_atomic-mt-x64;boost_atomic-mt;boost_atomic-mt;boost_atomic  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:2310 ] Searching for ATOMIC_LIBRARY_DEBUG: boost_atomic-vc143-mt-gd-x64-1_79;boost_atomic-vc143-mt-gd-x64;boost_atomic-vc143-mt-gd;boost_atomic-vc142-mt-gd-x64-1_79;boost_atomic-vc142-mt-gd-x64;boost_atomic-vc142-mt-gd;boost_atomic-vc141-mt-gd-x64-1_79;boost_atomic-vc141-mt-gd-x64;boost_atomic-vc141-mt-gd;boost_atomic-vc140-mt-gd-x64-1_79;boost_atomic-vc140-mt-gd-x64;boost_atomic-vc140-mt-gd;boost_atomic-mt-gd-x64-1_79;boost_atomic-mt-gd-x64;boost_atomic-mt-gd;boost_atomic-mt;boost_atomic  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:807 ] Boost_LIBRARY_DIR_DEBUG = "C:/boost_1_79_0/lib64-msvc-14.3"  
1> [CMake] -- [ C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.25/Modules/FindBoost.cmake:809 ] _boost_LIBRARY_SEARCH_DIRS_DEBUG = "C:/boost_1_79_0/lib64-msvc-14.3;NO_DEFAULT_PATH;NO_CMAKE_FIND_ROOT_PATH"  
1> [CMake] -- Found Boost: C:/boost_1_79_0 (found suitable version "1.79.0", minimum required is "1.79.0") found components: thread system chrono atomic  
1> [CMake] -- Configuring done  
1> [CMake] -- Generating done  
1> [CMake] -- Build files have been written to: C:/Users/...../out/build/x64-Debug  
```  
Note that I have enabled `Boost_DEBUG` and added `system` in the components  
Here's a sample of the problem with linking:  
```  
MSBuild version 17.5.0+6f08c67f3 for .NET Framework  
    
  MSBuild version 17.5.0+6f08c67f3 for .NET Framework  
    
    Checking Build System  
    Building Custom Rule C:/Users/...../CMakeLists.txt  
    JsdnInterface.cpp  
    JsdnMethods.cpp  
    JsdnFxrStart.cpp  
    JsdnActivator.cpp  
    JsdnDispatch.cpp  
    JsdnArgArray.cpp  
    JsdnConcurrent.cpp <--- The file that uses Boost  
    Generating code...  
C:\Users\....\out\build\x64-Debug\LINK : fatal error LNK1104: cannot open file 'libboost_thread-vc143-mt-gd-x64-1_79.lib'  
```  
  
> or for some reason cmake is configuring for mingw instead of msvc  
  
I have fully specified to use the Visual Studio 2022 generator and do not have any mingw installation.  
  
I've read and reread [FindBoost](https://cmake.org/cmake/help/latest/module/FindBoost.html) in the CMake documentation and didn't find anything useful to fix the problem.  
  
### EDIT  
I also tested this situation by changing the generator to Ninja and the problem also persists.

---

## Comment 3

> Username: Kojoley  
> Created at: 2023-04-23 13:42:50 UTC  
> Url: https://github.com/boostorg/thread/issues/385#issuecomment-1519071050  

Try compiling your project with `-DBOOST_ALL_NO_LIB`.

---

## Comment 4

> Username: jppgmx  
> Created at: 2023-04-23 15:27:27 UTC  
> Url: https://github.com/boostorg/thread/issues/385#issuecomment-1519093031  

I added the following:  
```cmake  
target_compile_definitions(ProjName PUBLIC -DBOOST_ALL_NO_LIB)  
target_link_libraries(ProjName PUBLIC Boost::thread)  
```  
It worked perfectly, just need to put the libraries in the folder to work, I've already created a utility to copy to the destination folder.   
  
Thanks for the help.

---

## Comment 5

> Username: Kojoley  
> Created at: 2023-04-23 15:36:38 UTC  
> Url: https://github.com/boostorg/thread/issues/385#issuecomment-1519094728  

> I added the following:  
>   
> ```cmake  
> target_compile_definitions(ProjName PUBLIC -DBOOST_ALL_NO_LIB)  
> target_link_libraries(ProjName PUBLIC Boost::thread)  
> ```  
  
You should be able to do this instead:  
  
```cmake  
target_link_libraries(ProjName PUBLIC Boost::thread Boost::disable_autolinking)  
```  
  
And it is likely a bug in Boost.Config that it uses `lib*` prefix for implibs on msvc, please file a bug to https://github.com/boostorg/config
