# #1210 - CMake testing problems [Closed]

> Username: pdimov  
> Created at: 2024-10-13 17:00:17 UTC  
> Updated at: 2024-10-17 17:39:35 UTC  
> Closed at: 2024-10-17 17:39:35 UTC  
> Url: https://github.com/boostorg/math/issues/1210  

I took a closer look at the CMake testing problem, whose most recent manifestation was this  
```  
[ 81%] Building CXX object libs/math/test/compile_test/CMakeFiles/boost_math-compile_tests.dir/tools_test_inc_test.cpp.o  
In file included from /home/runner/work/cmake/boost-root/libs/math/test/compile_test/tools_test_inc_test.cpp:12:  
/home/runner/work/cmake/boost-root/libs/math/test/compile_test/../../include_private/boost/math/tools/test.hpp:19:10: fatal error: boost/test/test_tools.hpp: No such file or directory  
   19 | #include <boost/test/test_tools.hpp>  
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
(https://github.com/boostorg/cmake/actions/runs/11310512254/job/31455650803)  
  
because I wondered why this doesn't also appear in the superproject CI which also invokes the CMake tests (https://github.com/boostorg/boost/actions/runs/11310570697/job/31455773234).  
  
It turns out that this is because the compile tests are added here  
  
https://github.com/boostorg/math/blob/522989011aca84e375e71b3a571007c2fbb9bd6d/test/compile_test/CMakeLists.txt  
  
directly (without using `boost_test`), which makes them (a) not dependencies of the `tests` target, which is what the Boost documented testing procedure builds (https://github.com/boostorg/cmake#testing-boost-with-cmake), and (b) included in the "all" target that is built by default by `make` or `cmake --build .`. (By Boost convention tests aren't included in "all", which makes it possible to build only the libraries even when testing is enabled.)  
  
The end result of all this is that `cmake --build . --target tests` works, because the compile tests are simply not built. Since those are compile-only, that's not a problem for the subsequent `ctest` invocation because there's nothing to run.  
  
`cmake --build .`, however, fails when testing is enabled, because it _does_ build the compile tests, and they fail.  
  
So...  
  
If you want to keep building the compile tests on `cmake --build .` but not on `cmake --build . --target tests`, you have to add `include_directories(../../include_private)` to `test/compile_test/CMakeLists.txt`;  
  
If you want to manually fix them to be built on `cmake --build . --target tests` but not `cmake --build .` per convention, you have to use `add_dependencies` manually to attach them to the `tests` target and use `EXCLUDE_FROM_ALL` and also add the `include_directories` fix;  
  
Or you can switch to using `boost_test`, which will take care of the `tests` target and `EXCLUDE_FROM_ALL`, in which case you can use its `INCLUDE_DIRECTORIES` argument instead of `include_directories`.  
  
(There's also the option of setting `BOOST_TEST_INCLUDE_DIRECTORIES` in `test/CMakeLists.txt`, which will apply to all subsequent `boost_test` and `boost_test_jamfile` invocations, but make sure to set it to an absolute path.)

---

## Comment 1

> Username: pdimov  
> Created at: 2024-10-13 17:11:41 UTC  
> Url: https://github.com/boostorg/math/issues/1210#issuecomment-2409054738  

(Actually the current error is caused not by missing `include_directories` but because the compile tests don't link to `Boost::unit_test_framework`.)

---

## Comment 2

> Username: mborland  
> Created at: 2024-10-15 13:59:21 UTC  
> Url: https://github.com/boostorg/math/issues/1210#issuecomment-2414003459  

Is there a way to glob with `boost_test`? It get an error with the following change to `test/compile_test/CMakeLists.txt`  
  
```  
include_directories(../../include_private)  
file(GLOB SRC_FILES "*.cpp")  
boost_test(SOURCES SRC_FILES COMPILE_FEATURES cxx_std_17 LINK_LIBRARIES Boost::math Boost::multiprecision Boost::numeric_ublas Boost::unit_test_framework )  
```  
  
The error is:  
```  
CMake Error at tools/cmake/include/BoostTest.cmake:242 (add_executable):  
  Cannot find source file:  
  
    SRC_FILES  
  
  Tried extensions .c .C .c++ .cc .cpp .cxx .cu .mpp .m .M .mm .ixx .cppm  
  .ccm .cxxm .c++m .h .hh .h++ .hm .hpp .hxx .in .txx .f .F .for .f77 .f90  
  .f95 .f03 .hip .ispc  
Call Stack (most recent call first):  
  libs/math/test/compile_test/CMakeLists.txt:7 (boost_test)  
  
  
CMake Error at tools/cmake/include/BoostTest.cmake:242 (add_executable):  
  No SOURCES given to target: boost_math-SRC_FILES  
Call Stack (most recent call first):  
  libs/math/test/compile_test/CMakeLists.txt:7 (boost_test)  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2024-10-15 14:11:51 UTC  
> Url: https://github.com/boostorg/math/issues/1210#issuecomment-2414039790  

Should be `boost_test(SOURCES ${SRC_FILES} ...` to expand the variable. Also, probably add CONFIGURE_DEPENDS to the glob.  
  
You can also use `foreach(src IN LISTS SRC_FILES)` if you want to add individual tests instead of one, although in this case it probably doesn't matter.
