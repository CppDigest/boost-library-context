# #137 - Compilation failure with Clang 17 and `-std=cpp23` [Open]

> Username: planetmarshall  
> Created at: 2024-06-08 12:57:08 UTC  
> Updated at: 2024-06-17 21:16:01 UTC  
> Url: https://github.com/boostorg/program_options/issues/137  

## Summary  
  
Including `<boost/program_options.hpp>` causes a compilation failure when compiling with clang 17.0.6 and GNU stdlibc++ 14 with `-std=gnu++23`  
  
## Steps to reproduce  
  
Install boost (a conanfile is provided) and extract the attached archive.  
  
[boost-po-test.tar.gz](https://github.com/user-attachments/files/15748101/boost-po-test.tar.gz)  
  
```  
tar -xf boost-po-test-tgz  
cd boost-po-test/  
cmake --preset default  
cmake --build build/default  
```  
  
## Expected result  
  
compilation is successful  
  
## Actual result  
  
compilation fails with  
  
```  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/tuple:956:36: error: no matching function for call to 'get'  
  956 |             return __convertible<decltype(std::get<_Is>(std::declval<_UTuple>()))...>();  
```  
  
## Workaround  
  
Edit the `CMakeLists.txt` file and change  
  
```  
set(CMAKE_CXX_STANDARD 23)  
```  
to  
```  
set(CMAKE_CXX_STANDARD 20)  
```  
  
and recompile.  
  
## System Info  
  
Boost 1.85.0 (conan)  
Arch Linux  
Clang 17.0.6  
GCC 14.1.1

---

## Comment 1

> Username: vprus  
> Created at: 2024-06-17 19:53:30 UTC  
> Url: https://github.com/boostorg/program_options/issues/137#issuecomment-2174307717  

Andrew, thanks for such a nice bug report.  
  
Sadly, I'm far away from C++ these days, so our only hope for a fix is if somebody else, or maybe you, writes a PR.  
  
Thanks again!

---

## Comment 2

> Username: planetmarshall  
> Created at: 2024-06-17 21:16:00 UTC  
> Url: https://github.com/boostorg/program_options/issues/137#issuecomment-2174438536  

No problem. Thanks for taking the time to look and your hard work on the library.
