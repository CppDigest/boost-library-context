# #196 - Tests do not build - cmake error [Closed]

> Username: kenobrien  
> Created at: 2014-07-18 15:29:48 UTC  
> Updated at: 2014-07-18 16:14:01 UTC  
> Closed at: 2014-07-18 15:55:18 UTC  
> Url: https://github.com/boostorg/compute/issues/196  

CMake produces an error in the tests directory. OpenSuse 13.1. Is there a specific version required to build?  
  
```  
ken@mjolnir:~/Projects/compute/test> cmake .  
-- The C compiler identification is GNU 4.8.1  
-- The CXX compiler identification is GNU 4.8.1  
-- Check for working C compiler: /usr/bin/cc  
-- Check for working C compiler: /usr/bin/cc -- works  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Boost version: 1.53.0  
-- Found the following Boost libraries:  
--   unit_test_framework  
CMake Error at CMakeLists.txt:16 (if):  
  if given arguments:  
  
    "AND" "GNU" "STREQUAL" "GNU"  
  
  Unknown arguments specified  
  
  
CMake Warning (dev) in CMakeLists.txt:  
  No cmake_minimum_required command is present.  A line of code such as  
  
    cmake_minimum_required(VERSION 2.8)  
  
  should be added at the top of the file.  The version specified may be lower  
  if you wish to support older CMake versions for this project.  For more  
  information run "cmake --help-policy CMP0000".  
This warning is for project developers.  Use -Wno-dev to suppress it.  
  
-- Configuring incomplete, errors occurred!  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-07-18 15:50:03 UTC  
> Url: https://github.com/boostorg/compute/issues/196#issuecomment-49446668  

It looks like you are trying to run cmake in the test directory. You should run cmake in the top-level directory (or in a separate build directory).  
  
Also see the developers guide in the wiki for more instructions on building the tests.

---

## Comment 2

> Username: kenobrien  
> Created at: 2014-07-18 15:55:18 UTC  
> Updated at: 2014-07-18 15:55:48 UTC  
> Url: https://github.com/boostorg/compute/issues/196#issuecomment-49447333  

You're correct. The developers wiki showed me how to correctly build the tests.   
  
https://github.com/kylelutz/compute/wiki/Developer's-Guide  
  
Issue resolved.

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-07-18 16:14:01 UTC  
> Url: https://github.com/boostorg/compute/issues/196#issuecomment-49449581  

Cool! Let me know if you run into any other issues.
