# #476 - CMake - "lib" prefix bug in implibs of MSVC [Open]

> Username: jppgmx  
> Created at: 2023-04-23 17:24:59 UTC  
> Updated at: 2023-05-15 15:53:11 UTC  
> Url: https://github.com/boostorg/config/issues/476  

### Introduction  
Recently, I reported an [issue](https://github.com/boostorg/thread/issues/385) in the `Boost.Thread` module where the msvc linker intended to link a file that doesn't even exist. The file was:  
  
```libboost_thread-vc143-mt-gd-x64-1_78.lib```  
  
It has also been reported, in the same issue, that the prefix `lib*` is used for mingw, even though I don't have any mingw installation and only have Visual Studio and msvc.  
  
### Solution of Bug  
The solution was to add `Boost::disable_autolinking` in `target_link_libraries`:  
```cmake  
target_link_libraries(ProjName PUBLIC Boost::thread Boost::disable_autolinking)  
```  
This solved the problem, but it wasn't supposed to have the `lib*` prefix when using CMake with msvc, it was to have linked correctly without having to specify `Boost::disable_autolinking`.  
  
The most likely cause would be a bug of this module that, when treating the prefix of the library on the Windows platform with msvc, ended up accidentally placing this prefix, being that it was supposed to be in mingw.  
  
### Extra Information  
- OS: Windows 11;  
- Arch: x64;  
- I'm using Visual Studio 2022;  
  - With Windows 10 SDK;  
  - With CMake 3.25.1-msvc1  
  - With MSVC toolset v143;  
- Boost 1.79 downloaded from SourceForge;  
- The files of Boost are located in `C:\boost_1_79_0`.  
  
The following instructions are used:  
```cmake  
if(PROJECT_DEBUG)  
    set(Boost_DEBUG             ON  )  
    set(Boost_USE_DEBUG_LIBS    ON  )  
    set(Boost_USE_RELEASE_LIBS  OFF )  
    set(Boost_USE_DEBUG_RUNTIME ON  )  
else()  
    set(Boost_USE_RELEASE_LIBS  ON  )  
    set(Boost_USE_DEBUG_LIBS    OFF )  
    set(Boost_USE_DEBUG_RUNTIME OFF )  
endif()  
  
set(Boost_USE_MULTITHREADED  ON  )  
set(Boost_USE_STATIC_LIBS    OFF )  
set(Boost_USE_STATIC_RUNTIME OFF )  
  
find_package(Boost 1.79.0 REQUIRED COMPONENTS thread system)  
```  
### Logs  
Configuration log:  
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
Build log:  
```  
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
  
### Steps to reproduce  
1. Create an empty CMake project with Visual Studio 2022 generator;  
2. Download the binaries from SourceForge and set `BOOST_ROOT`;  
3. Add those options listed above;  
4. Add `find_package` (as above), `include_directories` (`${Boost_INCLUDE_DIRS}`), `target_link_libraries` (`Boost::thread`);  
5. Wait compile and possibly give the error.  
  
_(I tried to be as brief as possible.)_

---

## Comment 1

> Username: Kojoley  
> Created at: 2023-05-09 22:08:55 UTC  
> Url: https://github.com/boostorg/config/issues/476#issuecomment-1540951461  

Could you please check that CMake adds `-DBOOST_DYN_LINK`? I couldn't find `BOOST_DYN_LINK` anywhere in CMake, which means it is a CMake bug. From my understanding it should've add it when you do `set(Boost_USE_STATIC_LIBS OFF)`.

---

## Comment 2

> Username: jppgmx  
> Created at: 2023-05-14 23:13:59 UTC  
> Url: https://github.com/boostorg/config/issues/476#issuecomment-1547023751  

I did the test adding:   
```cpp  
#ifndef BOOST_DYN_LINK  
    #error "Preprocessor not defined"  
#endif  
```  
Regardless of whether you add `Boost::disable_autolinking` or not, the macro is not defined.

---

## Comment 3

> Username: Kojoley  
> Created at: 2023-05-15 15:53:11 UTC  
> Url: https://github.com/boostorg/config/issues/476#issuecomment-1548121050  

Meh, you would not be able to test `BOOST_DYN_LINK` from the user code, it is being undefined here https://github.com/boostorg/config/blob/a1cf5d531405e62927b0257b5cbecc66a545b508/include/boost/config/auto_link.hpp#L520-L522  
`BOOST_DYN_LINK` seems to be an 'internal' macro that a library which uses `auto_link.hpp` configures, the user controlled macros are: `BOOST_ALL_DYN_LINK` and `BOOST_<WHATEVER>_DYN_LINK` (`BOOST_THREAD_DYN_LINK` in your case). It means that `set(Boost_USE_STATIC_LIBS OFF)` should either make CMake add `BOOST_ALL_DYN_LINK` on everything or `target_link_libraries(ProjName PUBLIC Boost::thread)` should add `BOOST_THREAD_DYN_LINK`.  
  
There are related tickets on CMake bug tracker:  
https://gitlab.kitware.com/cmake/cmake/-/issues/16484  
https://gitlab.kitware.com/cmake/cmake/-/issues/17241  
https://gitlab.kitware.com/cmake/cmake/-/issues/16699
