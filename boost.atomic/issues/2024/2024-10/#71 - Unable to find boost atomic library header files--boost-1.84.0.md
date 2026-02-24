# #71 - Unable to find boost atomic library header files//boost-1.84.0 [Closed]

> Username: BadganchiP1234  
> Created at: 2024-10-15 10:33:42 UTC  
> Updated at: 2024-10-19 17:37:06 UTC  
> Closed at: 2024-10-15 15:45:46 UTC  
> Url: https://github.com/boostorg/atomic/issues/71  

**ERROR:**  
Compilation failed Stdout: [ 50%] Building CXX object CMakeFiles/test_atomic.dir/atomicity.cpp.o Stderr: /sdk/sysroots/core2-64-poky-linux/usr/share/doc/boost-atomic/examples**/atomicity.cpp:38:10: fatal error: test_clock.hpp: No such file or directory 38 | #include "test_clock.hpp" | ^~~~~~~~~~~~~~~~ compilation terminated. make[2]: ***** [CMakeFiles/test_atomic.dir/build.make:76: CMakeFiles/test_atomic.dir/atomicity.cpp.o] Error 1 make[1]: *** [CMakeFiles/Makefile2:83: CMakeFiles/test_atomic.dir/all] Error 2 make: *** [Makefile:91: all] Error 2 Returncode: Expected: 0 Actual: 2  
  
Tried adding below path but still failing with same issue. Could you please help here?  
  
include_directories(  
${Boost_LIBRARY_DIRS}/atomic/test  
)

---

## Comment 1

> Username: Lastique  
> Created at: 2024-10-15 15:45:46 UTC  
> Updated at: 2024-10-15 15:46:09 UTC  
> Url: https://github.com/boostorg/atomic/issues/71#issuecomment-2414390856  

1. Boost.Atomic does not support testing from CMake. I'm assuming you're implementing the CMake support yourself. In that case I'm unlikely to be able to help much as I don't see what you're doing.  
2. `Boost_LIBRARY_DIRS` is the directories to look for libraries to link with. In `include_directories`, you need to add `<BOOST_ROOT>/libs/atomic/test`, where `<BOOST_ROOT>` is the directory where you unpacked the Boost package or checked out the Boost superproject. If you're writing `<BOOST_ROOT>/libs/atomic/test/CMakeLists.txt` then you can use `include_directories(${CMAKE_CURRENT_SOURCE_DIR})` in it.

---

## Comment 2

> Username: BadganchiP1234  
> Created at: 2024-10-17 14:19:09 UTC  
> Updated at: 2024-10-17 14:20:02 UTC  
> Url: https://github.com/boostorg/atomic/issues/71#issuecomment-2419687951  

@Lastique I had set below path as you mentioned in the above comment, but it is not able to find the header file.  
Anything i am doing wrong here?  
  
include_directories(  
    ${BOOST_ROOT}/libs/atomic/test  
)  
  
custom atomic cmake file content:  
  
**cmake_minimum_required(VERSION 2.8)  
  
FIND_PACKAGE(Boost  
             REQUIRED  
             COMPONENTS  
                 atomic  
)  
FIND_PACKAGE(Boost  
             REQUIRED  
             COMPONENTS  
                 thread  
)  
  
include_directories(  
    ${BOOST_ROOT}/libs/atomic/test  
)  
  
add_executable(test_atomic  
    atomicity.cpp  
)  
  
target_link_libraries(test_atomic  
    ${Boost_LIBRARIES}  
)**

---

## Comment 3

> Username: Lastique  
> Created at: 2024-10-17 14:33:08 UTC  
> Url: https://github.com/boostorg/atomic/issues/71#issuecomment-2419723272  

I don't know, you will have to debug this on your end. Check if `BOOST_ROOT` is being set correctly (it should probably be an absolute path).

---

## Comment 4

> Username: Lastique  
> Created at: 2024-10-19 17:37:05 UTC  
> Url: https://github.com/boostorg/atomic/issues/71#issuecomment-2424103025  

FYI, testing with CMake is now implemented in git.
