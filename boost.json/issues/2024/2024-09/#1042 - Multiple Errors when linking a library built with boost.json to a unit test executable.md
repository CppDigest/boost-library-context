# #1042 - Multiple Errors when linking a library built with boost.json  to a unit test executable [Closed]

> Username: DBizz22  
> Created at: 2024-09-17 12:16:35 UTC  
> Updated at: 2024-12-14 11:14:29 UTC  
> Closed at: 2024-12-14 11:14:28 UTC  
> Url: https://github.com/boostorg/json/issues/1042  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
### Version of Boost  
Version 1.85.0-4 from the msys2 mingw-w64-boost package  
<img width="955" alt="boost1" src="https://github.com/user-attachments/assets/4807ecfc-4b49-4ae5-9595-49712de86e9e">  
<img width="942" alt="boost2" src="https://github.com/user-attachments/assets/8e1c0602-61cb-420d-8956-9cd46e16c405">  
  
  
### Steps necessary to reproduce the problem  
CMakeLists.txt for build the unit test  
add_executable(unitTest UnitTest.cpp)  
target_link_libraries(unitTest  
    PRIVATE <target using boost.json> httpClientMock CppUTest::CppUTest  
    CppUTest::CppUTestExt)  
cpputest_discover_tests(unitTest)  
  
### All relevant compiler information  
g++ (Rev1, Built by MSYS2 project) 14.2.0  
  
I've tried building both the header-only build and builds dependent on the static boost_json library but they both produce the same similar errors.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-09-17 12:33:26 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355616852  

This looks like a problem with Boost.Container. If my guessing is correct, some Container header is missing an include. Can you please include `boost/container/detail/placement_new.hpp` instead of `boost/json/src.hpp`? Also, in the future could you please post copied text and not screenshots?

---

## Comment 2

> Username: DBizz22  
> Created at: 2024-09-17 12:37:55 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355631002  

ok, Here is the result  
[main] Building folder: C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/build/MinGW Debug Config   
[build] Starting build  
[proc] Executing command: C:\msys64\ucrt64\bin\cmake.EXE --build "C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/build/MinGW Debug Config" --target alphavantageApiTest --  
[build] [ 50%] Built target CppUTest  
[build] [100%] Built target CppUTestExt  
[build] [100%] Built target httpClientInterface  
[build] [100%] Built target coreApiInterface  
[build] [100%] Building CXX object bin/lib/api/alphavantageApi/CMakeFiles/alphavantageStatic.dir/alphavantage.cpp.obj  
[build] [100%] Linking CXX static library libalphavantageStatic.a  
[build] [100%] Built target alphavantageStatic  
[build] [100%] Built target httpClientMock  
[build] [100%] Building CXX object test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/forexApiUnitTest.cpp.obj  
[build] In file included from C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/src/lib/api/alphavantageApi/include/alphavantage.hpp:6,  
[build]                  from C:\Users\BIZZ\Documents\Vscode\Projects\Stock_Market_Simulator\test\alphavantageApiSuiteTest\forexApiUnitTest.cpp:7:  
[build] C:/msys64/ucrt64/include/boost/container/detail/placement_new.hpp:18:14: error: declaration of 'operator new' as non-function  
[build]    18 | inline void *operator new(std::size_t, void *p, boost_container_new_t)  
[build]       |              ^~~~~~~~  
[build] mingw32-make[3]: *** [test\alphavantageApiSuiteTest\CMakeFiles\alphavantageApiTest.dir\build.make:76: test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/forexApiUnitTest.cpp.obj] Error 1  
[build] mingw32-make[2]: *** [CMakeFiles\Makefile2:10538: test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/all] Error 2  
[build] mingw32-make[1]: *** [CMakeFiles\Makefile2:10545: test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/rule] Error 2  
[build] mingw32-make: *** [Makefile:4726: alphavantageApiTest] Error 2  
[proc] The command: C:\msys64\ucrt64\bin\cmake.EXE --build "C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/build/MinGW Debug Config" --target alphavantageApiTest -- exited with code: 2  
[driver] Build completed: 00:00:05.138  
[build] Build finished with exit code 2

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-09-17 12:40:35 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355639491  

Can you please post the contents of that file?

---

## Comment 4

> Username: DBizz22  
> Created at: 2024-09-17 12:43:10 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355647797  

#ifndef BOOST_CONTAINER_DETAIL_PLACEMENT_NEW_HPP  
#define BOOST_CONTAINER_DETAIL_PLACEMENT_NEW_HPP  
///////////////////////////////////////////////////////////////////////////////  
//  
// (C) Copyright Ion Gaztanaga 2014-2015. Distributed under the Boost  
// Software License, Version 1.0. (See accompanying file  
// LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// See http://www.boost.org/libs/container for documentation.  
//  
///////////////////////////////////////////////////////////////////////////////  
  
#include <cstddef>  
  
struct boost_container_new_t{};  
  
//avoid including <new>  
inline void *operator new(std::size_t, void *p, boost_container_new_t)  
{  return p;  }  
  
inline void operator delete(void *, void *, boost_container_new_t)  
{}  
  
#endif   //BOOST_CONTAINER_DETAIL_PLACEMENT_NEW_HPP

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-09-17 12:44:07 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355650534  

Is there really nothing in the line after `#include`?

---

## Comment 6

> Username: DBizz22  
> Created at: 2024-09-17 12:47:24 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355661341  

#include "cstddef"

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-09-17 13:02:09 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355711475  

Ok, now what happens if you don't include the Container header, and instead put the following?  
  
```c++  
#include <cstddef>  
std::size_t n;  
```  
  
BTW, if you want to put a bunch of code in a GH comment, use triple backticks (```) before and after it.

---

## Comment 8

> Username: DBizz22  
> Created at: 2024-09-17 13:07:01 UTC  
> Updated at: 2024-09-17 13:12:42 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355728068  

[main] Building folder: C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/build/MinGW Debug Config   
[build] Starting build  
[proc] Executing command: C:\msys64\ucrt64\bin\cmake.EXE --build "C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/build/MinGW Debug Config" --target alphavantageApiTest --  
[build] [ 50%] Built target CppUTest  
[build] [100%] Built target CppUTestExt  
[build] [100%] Built target httpClientInterface  
[build] [100%] Built target coreApiInterface  
[build] [100%] Building CXX object bin/lib/api/alphavantageApi/CMakeFiles/alphavantageStatic.dir/alphavantage.cpp.obj  
[build] [100%] Linking CXX static library libalphavantageStatic.a  
[build] [100%] Built target alphavantageStatic  
[build] [100%] Built target httpClientMock  
[build] [100%] Building CXX object test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/forexApiUnitTest.cpp.obj  
[build] [100%] Linking CXX executable alphavantageApiTest.exe  
[build] C:/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: ../../bin/lib/api/alphavantageApi/libalphavantageStatic.a(alphavantage.cpp.obj):C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/src/lib/api/alphavantageApi/include/alphavantage.hpp:7: multiple definition of `n'; CMakeFiles\alphavantageApiTest.dir/objects.a(forexApiUnitTest.cpp.obj):C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/src/lib/api/alphavantageApi/include/alphavantage.hpp:7: first defined here  
[build] collect2.exe: error: ld returned 1 exit status  
[build] mingw32-make[3]: *** [test\alphavantageApiSuiteTest\CMakeFiles\alphavantageApiTest.dir\build.make:104: test/alphavantageApiSuiteTest/alphavantageApiTest.exe] Error 1  
[build] mingw32-make[2]: *** [CMakeFiles\Makefile2:10538: test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/all] Error 2  
[build] mingw32-make[1]: *** [CMakeFiles\Makefile2:10545: test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/rule] Error 2  
[build] mingw32-make: *** [Makefile:4726: alphavantageApiTest] Error 2  
[proc] The command: C:\msys64\ucrt64\bin\cmake.EXE --build "C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/build/MinGW Debug Config" --target alphavantageApiTest -- exited with code: 2  
[driver] Build completed: 00:00:06.131  
[build] Build finished with exit code 2  
  
  
It builds successfully after hiding std::size_t n and the boost.json functions i used

---

## Comment 9

> Username: grisumbras  
> Created at: 2024-09-17 13:27:37 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355800062  

Now, please try  
  
```c++  
#include <cstddef>  
  
struct boost_container_new_t{};  
  
inline void *operator new(std::size_t, void *p, boost_container_new_t)  
{ return p; }  
```

---

## Comment 10

> Username: DBizz22  
> Created at: 2024-09-17 13:36:17 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355830028  

[main] Building folder: C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/build/MinGW Debug Config   
[build] Starting build  
[proc] Executing command: C:\msys64\ucrt64\bin\cmake.EXE --build "C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/build/MinGW Debug Config" --target alphavantageApiTest --  
[build] [  0%] Built target httpClientInterface  
[build] [  0%] Built target httpClientMock  
[build] [  0%] Built target coreApiInterface  
[build] [ 33%] Built target boost_container  
[build] [ 33%] Built target boost_json  
[build] [ 33%] Building CXX object bin/lib/api/alphavantageApi/CMakeFiles/alphavantageStatic.dir/alphavantage.cpp.obj  
[build] [ 33%] Linking CXX static library libalphavantageStatic.a  
[build] [ 33%] Built target alphavantageStatic  
[build] [ 66%] Built target CppUTest  
[build] [100%] Built target CppUTestExt  
[build] [100%] Building CXX object test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/forexApiUnitTest.cpp.obj  
[build] In file included from C:\Users\BIZZ\Documents\Vscode\Projects\Stock_Market_Simulator\test\alphavantageApiSuiteTest\forexApiUnitTest.cpp:7:  
[build] C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/src/lib/api/alphavantageApi/include/alphavantage.hpp:12:14: error: declaration of 'operator new' as non-function  
[build]    12 | inline void *operator new(std::size_t, void *p, boost_container_new_t)  
[build]       |              ^~~~~~~~  
[build] mingw32-make[3]: *** [test\alphavantageApiSuiteTest\CMakeFiles\alphavantageApiTest.dir\build.make:76: test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/forexApiUnitTest.cpp.obj] Error 1  
[build] mingw32-make[2]: *** [CMakeFiles\Makefile2:10542: test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/all] Error 2  
[build] mingw32-make[1]: *** [CMakeFiles\Makefile2:10549: test/alphavantageApiSuiteTest/CMakeFiles/alphavantageApiTest.dir/rule] Error 2  
[build] mingw32-make: *** [Makefile:4726: alphavantageApiTest] Error 2  
[proc] The command: C:\msys64\ucrt64\bin\cmake.EXE --build "C:/Users/BIZZ/Documents/Vscode/Projects/Stock_Market_Simulator/build/MinGW Debug Config" --target alphavantageApiTest -- exited with code: 2  
[driver] Build completed: 00:00:05.514  
[build] Build finished with exit code 2

---

## Comment 11

> Username: grisumbras  
> Created at: 2024-09-17 13:51:37 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355883100  

I am at a loss. Are you sure  
  
```c++  
#include <cstddef>  
std::size_t n = 1;  
```  
  
compiles?  
  
The only reason for your error I can imagine is that `<cstddef>` for some reason doesn't have a definition for `std::size_t`.  
  
BTW, what compiler flags are you using to build?

---

## Comment 12

> Username: pdimov  
> Created at: 2024-09-17 14:08:41 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355944628  

The problem is the macro definition at line 83 of CppUTest/MemoryLeakDetectorMacros.h as visible on the screenshot above.

---

## Comment 13

> Username: DBizz22  
> Created at: 2024-09-17 14:15:55 UTC  
> Updated at: 2024-09-17 14:16:12 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355975086  

> I am at a loss. Are you sure  
>   
> ```c++  
> #include <cstddef>  
> std::size_t n = 1;  
> ```  
>   
> compiles?  
>   
> The only reason for your error I can imagine is that `<cstddef>` for some reason doesn't have a definition for `std::size_t`.  
>   
> BTW, what compiler flags are you using to build?  
  
It compiles but with std::size_t commented out.  
only compiler flag effect :  
set(CXX_STANDARD 14)  
set(CMAKE_CXX_STANDARD_REQUIRED ON)

---

## Comment 14

> Username: grisumbras  
> Created at: 2024-09-17 14:21:08 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2355994288  

What @pdimov said. CppUTest defines a macro that messes up `operator new` declarations.

---

## Comment 15

> Username: DBizz22  
> Created at: 2024-09-17 14:47:22 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2356090720  

> What @pdimov said. CppUTest defines a macro that messes up `operator new` declarations.  
  
Please, do you have any suggestions?

---

## Comment 16

> Username: pdimov  
> Created at: 2024-09-17 15:06:44 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2356167315  

You need to include the CppUTest headers last, after the Boost.JSON headers (and probably after everything else to avoid similar issues elsewhere.)

---

## Comment 17

> Username: DBizz22  
> Created at: 2024-09-18 00:57:05 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2357276957  

> You need to include the CppUTest headers last, after the Boost.JSON headers (and probably after everything else to avoid similar issues elsewhere.)  
  
Unfortunately, that does not seem to fix the issue

---

## Comment 18

> Username: grisumbras  
> Created at: 2024-09-18 09:42:18 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2357990708  

Well, it should fix the issue of errors in Boost headers. The macro definition can't affect declarations before it. What's the error you're getting now?

---

## Comment 19

> Username: DBizz22  
> Created at: 2024-09-18 13:03:15 UTC  
> Updated at: 2024-09-18 13:05:04 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2358413955  

includes in the UnitTest.cpp  
```  
#include <iostream>  
#include <string>  
#include "httpClientMock.cpp"  
#include "alphavantage.hpp"  
#include <CppUTest/TestHarness.h>  
#include <CppUTest/CommandLineTestRunner.h>  
#include <CppUTestExt/MockSupport.h>  
```  
  
boost/json/src.hpp is declared in the alphavantage.hpp  
  
The errors are still the same

---

## Comment 20

> Username: grisumbras  
> Created at: 2024-10-09 13:40:16 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2402382671  

Does `#include "httpClientMock.cpp"` include any of the CppUTest headers?

---

## Comment 21

> Username: grisumbras  
> Created at: 2024-12-14 11:14:28 UTC  
> Url: https://github.com/boostorg/json/issues/1042#issuecomment-2543060838  

Closing as there was no response.
