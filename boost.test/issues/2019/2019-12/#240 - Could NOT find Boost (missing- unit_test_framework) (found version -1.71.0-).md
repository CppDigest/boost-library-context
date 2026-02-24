# #240 - Could NOT find Boost (missing: unit_test_framework) (found version   "1.71.0") [Closed]

> Username: Amjad95  
> Created at: 2019-12-05 10:51:30 UTC  
> Updated at: 2022-08-02 19:03:37 UTC  
> Closed at: 2019-12-06 11:33:19 UTC  
> Url: https://github.com/boostorg/test/issues/240  

cmake_minimum_required(VERSION 3.15)  
project(My_String)  
  
set(CMAKE_CXX_STANDARD 17)  
set(Boost_USE_STATIC_LIBS OFF)  
set(SOURCE_FILES MyStringTest.cpp)  
set(BOOST_ROOT "C:\\Program Files\\boost\\boost_1_71_0")  
set(BOOST_LIBRARY_DIR "C:\\Program Files\\Boost\\boost_1_71_0\\stage\\lib")  
  
find_package(Boost REQUIRED COMPONENTS unit_test_framework)  
  
include_directories(${Boost_INCLUDE_DIR})  
include_directories(../src)  
  
add_executable (Boost_Tests_run ${SOURCE_FILES})  
  
target_link_libraries (Boost_Tests_run Boost::unit_test_framework)  
  
  
I have built boost lib and I need to use unit_test_framework. I am using the module from cmake to find the lib. but I get this error:  
  
CMake Error at C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindPackageHandleStandardArgs.cmake:137 (message):  
  Could NOT find Boost (missing: unit_test_framework) (found version  
  "1.71.0")

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-12-05 16:42:08 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562211244  

Hi,  
There is an example on how to use Boost.Test with cmake [there](https://www.boost.org/doc/libs/1_71_0/libs/test/doc/html/boost_test/section_faq.html#boost_test.section_faq.how_to_set_up_a_cmake_project_us). Please have a look and let me know if this solves your issue.

---

## Comment 2

> Username: Amjad95  
> Created at: 2019-12-05 17:20:30 UTC  
> Updated at: 2019-12-05 17:21:19 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562227443  

Hi, Thank you for your support! I have modified cmakelists.txt file as in the example suggested:  
============================  
cmake_minimum_required(VERSION 3.15)  
project(My_String)  
enable_testing()  
  
set(CMAKE_CXX_STANDARD 17)  
set(SOURCE_FILES MyStringTest.cpp)  
  
find_package(Boost 1.71.0 REQUIRED COMPONENTS unit_test_framework)  
  
# creates the executable  
add_executable(Boost_Tests_run ${SOURCE_FILES})  
  
# indicates the include paths  
target_include_directories(Boost_Tests_run PRIVATE ${Boost_INCLUDE_DIRS})  
  
# indicates the shared library variant  
target_compile_definitions(Boost_Tests_run PRIVATE "BOOST_TEST_DYN_LINK=1")  
  
# indicates the link paths  
target_link_libraries(Boost_Tests_run ${Boost_UNIT_TEST_FRAMEWORK_LIBRARY})  
======================================  
I still get the same error!

---

## Comment 3

> Username: Amjad95  
> Created at: 2019-12-05 17:28:18 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562230700  

C:\Users\aliss\AppData\Local\JetBrains\Toolbox\apps\CLion\ch-0\193.5233.103\bin\cmake\win\bin\cmake.exe -DCMAKE_BUILD_TYPE=Debug -G "CodeBlocks - MinGW Makefiles" F:\SoftwareProjects\CLionProjects\C++\My_String  
CMake Error at C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindPackageHandleStandardArgs.cmake:137 (message):  
  Could NOT find Boost (missing: unit_test_framework) (found suitable version  
  "1.71.0", minimum required is "1.71.0")  
Call Stack (most recent call first):  
  C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindPackageHandleStandardArgs.cmake:378 (_FPHSA_FAILURE_MESSAGE)  
  C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:2161 (find_package_handle_standard_args)  
  test/CMakeLists.txt:10 (find_package)  
  
  
-- Configuring incomplete, errors occurred!  
See also "F:/SoftwareProjects/CLionProjects/C++/My_String/cmake-build-debug/CMakeFiles/CMakeOutput.log".  
  
[Finished]  
  
This is the complete output, if it helps!

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-12-06 02:13:30 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562402216  

I believe you also have to specify the `BOOST_ROOT` variable in CMake. See [this](https://cmake.org/cmake/help/latest/module/FindBoost.html) for details about the variable.

---

## Comment 5

> Username: Amjad95  
> Created at: 2019-12-06 06:49:55 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562455229  

I have done that, still no success!!  
I have been trying for more than a week, Looking in the internet, trying every sort of thing without being able to fix it.   
Could you please explain the problem? why cmake is able to find boost but not able to find the component? because it clearly says that: I have enabled debugging to see more details:  
  
C:\Users\aliss\AppData\Local\JetBrains\Toolbox\apps\CLion\ch-0\193.5233.103\bin\cmake\win\bin\cmake.exe -DCMAKE_BUILD_TYPE=Debug -G "CodeBlocks - MinGW Makefiles" F:\SoftwareProjects\CLionProjects\C++\My_String  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1446 ] _boost_TEST_VERSIONS = "1.71.0;1.71"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1447 ] Boost_USE_MULTITHREADED = "TRUE"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1448 ] Boost_USE_STATIC_LIBS = <unset>  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1449 ] Boost_USE_STATIC_RUNTIME = <unset>  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1450 ] Boost_ADDITIONAL_VERSIONS = <unset>  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1451 ] Boost_NO_SYSTEM_PATHS = <unset>  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1520 ] BOOST_ROOT = "C:\Program Files\Boost_Built\boost_1_71_0"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1521 ] ENV{BOOST_ROOT} = <unset>  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1522 ] BOOST_INCLUDEDIR = <unset>  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1523 ] ENV{BOOST_INCLUDEDIR} = <unset>  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1524 ] BOOST_LIBRARYDIR = <unset>  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1525 ] ENV{BOOST_LIBRARYDIR} = <unset>  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1613 ] location of version.hpp: C:/Program Files/boost/boost_1_71_0/boost/version.hpp  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1653 ] Boost_VERSION = "1.71.0"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1654 ] Boost_VERSION_STRING = "1.71.0"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1655 ] Boost_VERSION_MACRO = "107100"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1656 ] Boost_VERSION_MAJOR = "1"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1657 ] Boost_VERSION_MINOR = "71"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1658 ] Boost_VERSION_PATCH = "0"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1659 ] Boost_VERSION_COUNT = "3"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1676 ] Boost_LIB_PREFIX = ""  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1677 ] Boost_NAMESPACE = "boost"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:787 ] _boost_COMPILER = "-mgw81" (guessed)  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1713 ] _boost_MULTITHREADED = "-mt"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1790 ] _boost_ARCHITECTURE_TAG = "" (detected)  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1794 ] _boost_RELEASE_ABI_TAG = "-"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1795 ] _boost_DEBUG_ABI_TAG = "-d"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1855 ] _boost_LIBRARY_SEARCH_DIRS_RELEASE = "C:\Program Files\Boost_Built\boost_1_71_0/lib;C:\Program Files\Boost_Built\boost_1_71_0/stage/lib;C:/Program Files/boost/boost_1_71_0/lib;C:/Program Files/boost/boost_1_71_0/../lib;C:/Program Files/boost/boost_1_71_0/stage/lib;PATHS;C:/boost/lib;C:/boost;/sw/local/lib"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:1856 ] _boost_LIBRARY_SEARCH_DIRS_DEBUG = "C:\Program Files\Boost_Built\boost_1_71_0/lib;C:\Program Files\Boost_Built\boost_1_71_0/stage/lib;C:/Program Files/boost/boost_1_71_0/lib;C:/Program Files/boost/boost_1_71_0/../lib;C:/Program Files/boost/boost_1_71_0/stage/lib;PATHS;C:/boost/lib;C:/boost;/sw/local/lib"  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:2040 ] Searching for UNIT_TEST_FRAMEWORK_LIBRARY_RELEASE: boost_unit_test_framework-mgw81-mt-1_71;boost_unit_test_framework-mgw81-mt;boost_unit_test_framework-mgw81-mt;boost_unit_test_framework-mt-1_71;boost_unit_test_framework-mt;boost_unit_test_framework-mt;boost_unit_test_framework-mt;boost_unit_test_framework  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:2095 ] Searching for UNIT_TEST_FRAMEWORK_LIBRARY_DEBUG: boost_unit_test_framework-mgw81-mt-d-1_71;boost_unit_test_framework-mgw81-mt-d;boost_unit_test_framework-mgw81-mt-d;boost_unit_test_framework-mt-d-1_71;boost_unit_test_framework-mt-d;boost_unit_test_framework-mt-d;boost_unit_test_framework-mt;boost_unit_test_framework  
CMake Error at C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindPackageHandleStandardArgs.cmake:137 (message):  
  Could NOT find Boost (missing: unit_test_framework) (found suitable version  
  "1.71.0", minimum required is "1.71.0")  
Call Stack (most recent call first):  
  C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindPackageHandleStandardArgs.cmake:378 (_FPHSA_FAILURE_MESSAGE)  
  C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:2161 (find_package_handle_standard_args)  
  test/CMakeLists.txt:13 (find_package)  
  
  
-- Configuring incomplete, errors occurred!  
See also "F:/SoftwareProjects/CLionProjects/C++/My_String/cmake-build-debug/CMakeFiles/CMakeOutput.log".  
  
[Finished]

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-12-06 08:10:24 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562475524  

I am seeing now that you are using `mingw`. It is searching for those library names, in order:  
  
```  
-- [ C:/Users/aliss/AppData/Local/JetBrains/Toolbox/apps/CLion/ch-0/193.5233.103/bin/cmake/win/share/cmake-3.15/Modules/FindBoost.cmake:2095 ] Searching for UNIT_TEST_FRAMEWORK_LIBRARY_DEBUG: boost_unit_test_framework-mgw81-mt-d-1_71;boost_unit_test_framework-mgw81-mt-d;boost_unit_test_framework-mgw81-mt-d;boost_unit_test_framework-mt-d-1_71;boost_unit_test_framework-mt-d;boost_unit_test_framework-mt-d;boost_unit_test_framework-mt;boost_unit_test_framework  
```  
  
is there  any library like this physically present in your drive under `BOOST_ROOT/lib` ? Also if you built boost with `b2`, you should specify the `mingw` toolchain on the command line. I believe the default is to use Visual Studio command line tools, if installed, and the generated libraries should not be compatible with `mingw`.

---

## Comment 7

> Username: Amjad95  
> Created at: 2019-12-06 08:48:19 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562486617  

I am aware that The compiler is mingw, and It is the only compiler installed on the machine. and I have bulit boost using this compiler.  It is already specified in tool chain.   
Here is a screen shots of my boost dirs,   
[Screen Shot.docx](https://github.com/boostorg/test/files/3931181/Screen.Shot.docx)

---

## Comment 8

> Username: raffienficiaud  
> Created at: 2019-12-06 10:26:06 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562518856  

> I am aware that The compiler is mingw, and It is the only compiler installed on the machine. and I have bulit boost using this compiler. It is already specified in tool chain.  
  
Good.  
  
> Here is a screen shots of my boost dirs,  
> [Screen Shot.docx](https://github.com/boostorg/test/files/3931181/Screen.Shot.docx)  
  
Coming back to the original question "is there any library like this physically present in your drive under BOOST_ROOT/lib ?", the answer seems to be simply "no". See the `-x86` post-fix for instance.   
  
Please have a look at the CMake variable [`Boost_ARCHITECTURE`](https://cmake.org/cmake/help/latest/module/FindBoost.html), indicate for instance `-x64` if this is a 64 bit build, and check again what `FindBoost` tries to find (the type of logs that you sent me).

---

## Comment 9

> Username: Amjad95  
> Created at: 2019-12-06 11:32:35 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562538592  

Some one suggested to use vcpkg mananger https://github.com/microsoft/vcpkg  
And So I gave it a try, I faced couple of issues but after that It worked so what I did is:  
  
1. install vcpkg https://github.com/microsoft/vcpkg (Follow the instructions)  
You migh have the error when setting up (Fatal error, could not do post-extract operation renaming 'File' to 'Different name' ) You need to rename the manually.  
  
2. install boost-test lib (you will see instruction on how to install packages) on the home page of vcpkg  
  
3. you need to set these variables for cmake:  
 -DVCPKG_TARGET_TRIPLET=x86-windows (x86-windows  in my case)  
"-DCMAKE_TOOLCHAIN_FILE='root to vcpkg'/scripts/buildsystems/vcpkg.cmake" (in my case root to vcpkg = F:/Files/vcpkg  
so the variable will be:  
"-DCMAKE_TOOLCHAIN_FILE=F:/Files/vcpkg/scripts/buildsystems/vcpkg.cmake"  
  
4. in your test/cmakelists.txt file you need to add the following:  
find_package (Boost REQUIRED COMPONENTS unit_test_framework)  
target_link_libraries (your_test_exe Boost::unit_test_framework)  
in my case I have my test/cmakelists.txt like this:  
cmake_minimum_required(VERSION 3.15)  
project(My_String)  
  
set(CMAKE_CXX_STANDARD 17)  
set(CMAKE_CXX_FLAGS "--coverage" )  
set(SOURCE_FILES MyStringTest.cpp)  
set(Boost_DEBUG ON)  
  
find_package (Boost REQUIRED COMPONENTS unit_test_framework)  
  
include_directories(../Src)  
  
add_executable (Boost_Tests_run ${SOURCE_FILES})  
  
target_link_libraries (Boost_Tests_run Boost::unit_test_framework)  
  
5. In your test.cpp you need to include:  
#include <boost/test/included/unit_test.hpp> as it is!  
in addition to the classes you are testing.  
in my case it looks like this:  
#define BOOST_TEST_DYN_LINK  
#define BOOST_TEST_MAIN  
#define BOOST_TEST_MODULE MyString_Test_Suite  
  
#include <iostream>  
#include "MyString.h"  
#include "MyString.cpp"  
#include <boost/test/included/unit_test.hpp>  
  
After more than a week of googling, trial and error this finally worked for me!  
Hope it helps if any one face the same issue.  
Thank you raffienficiaud for your support!

---

## Comment 10

> Username: raffienficiaud  
> Created at: 2019-12-06 11:50:53 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562543136  

Thanks @Amjad95 for the info, but I am confident in saying that you are just facing a CMake issue,  and many developers use the same stack as you do. I tried to guide you through a resolution that is not involving any extra tool, the reason being  that many people would not want to use a package management tool like `vcpkg`, or even an extra tool at all.   
I would have preferred to solve the initial issue and I am feeling a bit frustrated :)  
  
The nice thing is that `vcpkg` is doing the job right!

---

## Comment 11

> Username: raffienficiaud  
> Created at: 2019-12-06 11:54:40 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-562544078  

From my previous comment, don't get me wrong: I am happy that you solved your issue and you are able to use boost.test!

---

## Comment 12

> Username: dimonic  
> Created at: 2021-03-01 17:13:00 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-788117604  

Yeah, we are getting the same issue and I don't see why I should need some external package to resolve it.

---

## Comment 13

> Username: dimonic  
> Created at: 2021-03-01 17:36:52 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-788134475  

OK, so I was able to fix my problem - I was using a "cmake reserved" name for a library component variable. I changed the name "CMAKE_SYSROOT" to "CMAKE_LIBROOT" and it worked for me.

---

## Comment 14

> Username: jirikral  
> Created at: 2021-06-20 10:02:00 UTC  
> Updated at: 2021-06-20 10:02:15 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-864529297  

My solution to the same error message was: apt-get install libboost-all-dev

---

## Comment 15

> Username: lee-merrill  
> Created at: 2022-08-02 19:03:37 UTC  
> Url: https://github.com/boostorg/test/issues/240#issuecomment-1203104344  

My solution to this was to create a symbolic link in /usr/lib64: cd /usr/lib64; ln -s libboost_unit_test_framework.so.1.66.0 libboost_unit_test_framework.so
