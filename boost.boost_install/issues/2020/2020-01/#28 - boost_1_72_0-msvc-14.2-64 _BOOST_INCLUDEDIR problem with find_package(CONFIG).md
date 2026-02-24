# #28 - boost_1_72_0-msvc-14.2-64 _BOOST_INCLUDEDIR  problem with find_package(CONFIG) [Closed]

> Username: drywolf  
> Created at: 2020-01-22 14:53:02 UTC  
> Updated at: 2020-05-06 19:23:12 UTC  
> Closed at: 2020-05-06 19:23:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28  

hi  
I am trying to use the latest boost release with CMake find_package(CONFIG), but the boost cmake config seems to incorrectly define the interface include directories that are then applied for my executable that links to boost.  
  
Boost is installed (by default) in C:\local\boost_1_72_0 on windows.  
The boost cmake config that resolves the header include directories is in:  
C:\local\boost_1_72_0\lib64-msvc-14.2\cmake\boost_headers-1.72.0\boost_headers-config.cmake  
The problem is that at the end of the config `_BOOST_INCLUDEDIR` points to `C:\local` when it should actually point to `C:\local\boost_1_72_0`.  
  
If in `boost_headers-config.cmake` I change the line:  
`get_filename_component(_BOOST_INCLUDEDIR "${_BOOST_CMAKEDIR}/../../../" ABSOLUTE)`  
to:  
`get_filename_component(_BOOST_INCLUDEDIR "${_BOOST_CMAKEDIR}/../../" ABSOLUTE)`  
... then I can compile my project without problems.  
With the original line above I can see that the incorrect include directory is set in Visual Studio and hence I get errors that the boost includes can not be found.  
  
PS: I am not sure if I am using the find_package() syntax correctly with regard to boost, especially how to declare the path that find_package() should use to look for boost configs, so please correct me if I am doing something wrong on that end. Thanks  
  
A minimal reproduction of what I'm doing looks like this:  
  
**CMakeLists.txt**  
```  
cmake_minimum_required(VERSION 3.16)  
project(cmake-boost-test)  
  
# set(Boost_DEBUG TRUE)  
# set(Boost_USE_STATIC_LIBS OFF)  
  
find_package(Boost 1.72.0 EXACT COMPONENTS date_time REQUIRED CONFIG PATHS "C:/local/boost_1_72_0/lib64-msvc-14.2/cmake")  
  
add_executable(${PROJECT_NAME} main.cpp)  
target_link_libraries(${PROJECT_NAME} PUBLIC  
    Boost::date_time  
)  
```  
**main.cpp**  
```  
#include <iostream>  
#include <boost/date_time.hpp>  
  
using namespace boost::gregorian;  
  
int main(int argc, char** argv)  
{  
    std::cout << "hello world!" << std::endl;  
  
    // test boost::date_time  
    std::string s("2001-10-9"); //2001-October-09  
    date d(from_simple_string(s));  
    std::cout << to_simple_string(d) << std::endl;  
  
    return 0;  
}  
```

---

## Comment 1

> Username: hesmar  
> Created at: 2020-01-23 12:27:49 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-577660464  

I have the same issue with the boost_1_72_0-msvc-14.1-64 build.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-01-24 04:45:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-577989212  

How has Boost been built and installed?

---

## Comment 3

> Username: pdimov  
> Created at: 2020-01-24 04:58:57 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-577991617  

Ah I suppose this is about the binary package.  
  
@teeks99  
  
Tom, how is the package built? It looks like the directories are moved after staging, so the CMake config ends up confused?

---

## Comment 4

> Username: teeks99  
> Created at: 2020-01-24 12:05:03 UTC  
> Updated at: 2020-01-24 12:05:37 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-578104289  

Yes, we move things around after [building the binaries](https://github.com/teeks99/boost-release-windows/blob/master/run.py). The problem is that the `--stagedir` option doesn't give you full control of the path it outputs to. I.e. we want :  
```  
\boost_1_72_0  
|-- boost  
|   |-- headers, etc  
|-- doc  
|-- lib32-msvc-10.0  
|   |-- lib files  
|   |-- dll files  
|-- lib64-msvc-10.0  
|-- lib32-msvc-12.0  
|-- libs  
|-- tools  
  
```  
  
But if you specify a stagedir for the address-modle=32 toolset=msvc10.0 build as `lib32-msvc-10.0`, then files go into `lib32-msvc-10.0\lib` :-(  
  
Apparently the CMake files are also getting put into the lib dir now? (e.g. `boost_1_72_0/lib32-msvc-10.0/cmake`)  I'm not sure how to attack this problem, I'm not much of a cmake expert...can I configure something before the build to identify what the path *will* be?

---

## Comment 5

> Username: pdimov  
> Created at: 2020-01-24 14:59:07 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-578165070  

There's full control where to put the various files when doing `b2 install`, but when staging with `b2 stage`, the only variable is the stagedir. By default, the include directory remains at `.` (as the headers are in `boost/`) and the libraries get put into `stage/lib`. So the include directory is two levels up from the library directory, and that's what gets "encoded" in the CMake configurations.  
  
Moving `stage/lib` to `lib64-msvc-14.2` means that the include directory is now only one level up, and the CMake configs no longer compute it correctly.  
  
The two ways I see to solve this are either to use `b2 install`, which has options for every directory, or to keep staging, but with a stagedir of `.` instead of `stage`.

---

## Comment 6

> Username: pdimov  
> Created at: 2020-01-24 15:04:57 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-578167608  

(We now have the option to put 32/64 in the same directory too; not sure if we should though.)

---

## Comment 7

> Username: pdimov  
> Created at: 2020-01-24 19:04:36 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-578258983  

Or, I suppose, we can add separate `--stage-libdir` and `--stage-bindir` options for `stage/lib` and `stage/bin`, respectively. I'll need to go over all uses of `--stagedir` to verify that this wouldn't create any issues.

---

## Comment 8

> Username: pdimov  
> Created at: 2020-02-19 01:33:56 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-587990002  

OK.  
  
1. I implemented the `--stage-libdir` option, allowing one to override the library directory for staging.  
2. I verified that `--stagedir=.` works and puts the libraries into `./lib`, which is at the right nesting level.  
  
So, there are two possible ways to fix this issue. Either stage directly into f.ex. `lib64-msvc-14.2` with `--stage-libdir=lib64-msvc-14.2`, or use `--stagedir=.` and then rename `lib` to `lib64-msvc-14.2`.  
  
Tom, please let me know if that's enough information to go forward with a fix, or whether I can help in some other way.

---

## Comment 9

> Username: teeks99  
> Created at: 2020-02-25 12:40:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-590846969  

I've updated my build script to use `--stage-libdir`, it looks like things are working well. Could you please check the output in the link below (1.3GB) and verify it is happy with CMake, I don't have an easy way to test that.  
  
https://boost.teeks99.com/temp/boost_1_73_0-snapshot-bin-msvc-all-32-64--stage-lib.7z

---

## Comment 10

> Username: pdimov  
> Created at: 2020-02-26 13:24:02 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-591423699  

This works for me, with the following CMakeLists.txt:  
```  
cmake_minimum_required(VERSION 3.5...3.16)  
  
project(CmakeConfigFilesystemTest LANGUAGES CXX)  
  
find_package(Boost 1.73.0 EXACT CONFIG REQUIRED COMPONENTS filesystem)  
  
add_executable(main quick.cpp)  
target_link_libraries(main Boost::filesystem)  
  
enable_testing()  
add_custom_target(check COMMAND ${CMAKE_CTEST_COMMAND} --output-on-failure -C $<CONFIG>)  
  
add_test(main main)  
```  
and quick.cpp:  
```  
#include <boost/filesystem.hpp>  
#include <boost/core/lightweight_test.hpp>  
  
namespace fs = boost::filesystem;  
  
int main()  
{  
    fs::path p1( "a" );  
    fs::path p2 = p1 / "b";  
  
    BOOST_TEST_EQ( p2, "a/b" );  
  
    return boost::report_errors();  
}  
```  
and command lines  
```  
cmake -DBoost_ROOT=D:\boost_1_73_0\lib64-msvc-14.2\cmake -DBoost_VERBOSE=1 ..  
```  
```  
cmake --build .  
```  
```  
cmake --build . --target check  
```  
@drywolf, @hesmar: let us know if that package works for you.

---

## Comment 11

> Username: drywolf  
> Created at: 2020-03-11 23:04:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/28#issuecomment-597922952  

I just tested it with `lib64-msvc-14.2` and everything seems to work as expected now.
