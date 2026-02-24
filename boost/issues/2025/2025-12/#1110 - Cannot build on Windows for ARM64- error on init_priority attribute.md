# #1110 - Cannot build on Windows for ARM64: error on init_priority attribute. [Open]

> Username: carlo-bramini  
> Created at: 2025-12-12 09:25:34 UTC  
> Updated at: 2025-12-12 09:58:47 UTC  
> Url: https://github.com/boostorg/boost/issues/1110  

I tried to compile Boost libraries with `aarch64-w64-mingw32` cross compiler.  
Unfortunately, the build process failed after a while with this error message:  
```  
/home/carlo/packages/boost/boost-1.66.0-1.src/boost-1.90.0-1.noarch/src/boost-1.90.0/libs/nowide/src/iostream.cpp:148:70: error: 'init_priority' attribute ignored [-Werror=attributes]  
  148 | #define BOOST_NOWIDE_INIT_PRIORITY __attribute__((init_priority(101)))  
      |                                                                      ^  
```  
At the time of writing, the support for `init_priority` is tested into two CMakeLists.txt files:  
  
- libs/filesystem/CMakeLists.txt  
- libs/nowide/CMakeLists.txt  
  
Inside these scripts, `check_cxx_source_compiles()` is used.  
These calls check if the test compiles fine and they define `BOOST_NOWIDE_HAS_INIT_PRIORITY` and `BOOST_FILESYSTEM_HAS_INIT_PRIORITY` if everything looks ok.  
However, these tests do not fail if there is a just warning.  
For example, I tried to compile the test source myself and I got this:  
```  
$ aarch64-w64-mingw32-g++ boost-1.90.0-1.noarch/src/boost-1.90.0/libs/nowide/config/check_attribute_init_priority.cpp -c -o c.o  
boost-1.90.0-1.noarch/src/boost-1.90.0/libs/nowide/config/check_attribute_init_priority.cpp:9:43: warning: 'init_priority' attribute ignored [-Wattributes]  
    9 | Foo foo __attribute__((init_priority(101)));  
      |                                           ^  
```  
So, for CMake the test was good anyways.  
The problem comes out because the `-Werror` option, which makes the build process to fail later.  
  
I have not enough knowledge at the moment, but I can see these two solutions:  
Adding `-Wno-error=attributes` (if supported) when building those libraries, or  
Adding `-Werror` (if supported) when compiling the tests with `check_cxx_source_compiles()`.  
  
This issue happens when building for Windows on ARM64 with GCC.  
The `i686-w64-mingw32` and `x86_64-w64-mingw32` cross compilers also work with that attribute, but they don't emit any warning and that's why this issue was not seen before.   
  
I hope that you will find this report useful.  
  
  
EDIT: it looks like these scripts already handle `CMAKE_REQUIRED_FLAGS` before calling `check_cxx_source_compiles()`:  
  
- libs/atomic/CMakeLists.txt  
- libs/log/CMakeLists.txt  
  
Probably, it would be enough to follow the same way for adding the needed option.
