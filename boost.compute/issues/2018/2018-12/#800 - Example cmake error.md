# #800 - Example cmake error [Closed]

> Username: mamat-rahmat  
> Created at: 2018-12-05 07:23:16 UTC  
> Updated at: 2018-12-06 03:20:33 UTC  
> Closed at: 2018-12-06 03:20:33 UTC  
> Url: https://github.com/boostorg/compute/issues/800  

I tried to build the examples  
Here is the error message  
```  
-- The C compiler identification is GNU 5.4.0  
-- The CXX compiler identification is GNU 5.4.0  
-- Check for working C compiler: /usr/bin/cc  
-- Check for working C compiler: /usr/bin/cc -- works  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
CMake Error at CMakeLists.txt:44 (if):  
  if given arguments:  
  
    "AND" "NOT"  
  
  Unknown arguments specified  
  
  
CMake Warning (dev) in CMakeLists.txt:  
  No cmake_minimum_required command is present.  A line of code such as  
  
    cmake_minimum_required(VERSION 3.5)  
  
  should be added at the top of the file.  The version specified may be lower  
  if you wish to support older CMake versions for this project.  For more  
  information run "cmake --help-policy CMP0000".  
This warning is for project developers.  Use -Wno-dev to suppress it.  
  
-- Configuring incomplete, errors occurred!  
See also "/home/coderbodoh/Documents/compute/example/CMakeFiles/CMakeOutput.log".  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-12-05 08:54:49 UTC  
> Updated at: 2018-12-05 08:55:24 UTC  
> Url: https://github.com/boostorg/compute/issues/800#issuecomment-444407888  

You can not use `CMakeLists.txt` from `example/` as a standalone CMake script, it's adjusted to be included by the main `CMakeLists.txt` script.
