# #35 - Boost\libs\ptr_container failed to build on MSVC [Closed]

> Username: fangzhouxia  
> Created at: 2022-08-26 01:58:23 UTC  
> Updated at: 2023-02-23 15:31:06 UTC  
> Closed at: 2023-02-23 15:31:06 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/35  

**Issue description**:  
Boost\libs\ptr_container failed to build on MSVC. Could you please take a look? We ues https://github.com/boostorg/boost/commit/0724ce0.  
  
**Build step**:  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git boost  
2. open a VS 2019 x64 command prompt and browse to boost  
3. \bootstrap  
4. \b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. \b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. \b2 -j4 variant=release --build-dir=..\out\x64rel libs\ptr_container \test  
**Error info**:  
  
Running 1 test case...  
libs/ptr_container/test/serialization.cpp(206): error: in "Pointer Container Test Suite/test_serialization": check (*vec2.begin()).i == -1 has failed [0 != -1]  
libs/ptr_container/test/serialization.cpp(207): error: in "Pointer Container Test Suite/test_serialization": check (*++vec2.begin()).i == 0 has failed [-1 != 0]  
libs/ptr_container/test/serialization.cpp(206): error: in "Pointer Container Test Suite/test_serialization": check (*vec2.begin()).i == -1 has failed [0 != -1]  
libs/ptr_container/test/serialization.cpp(207): error: in "Pointer Container Test Suite/test_serialization": check (*++vec2.begin()).i == 0 has failed [-1 != 0]  
**Detail log**:  
[test.log.97.txt](https://github.com/boostorg/boost/files/9429208/test.log.97.txt)
