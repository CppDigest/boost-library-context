# #314 - Could not link fiber 1.84.0 as shared library on Windows 10 [Closed]

> Username: uilianries  
> Created at: 2024-01-05 09:31:25 UTC  
> Updated at: 2024-08-28 10:52:01 UTC  
> Closed at: 2024-08-28 10:52:01 UTC  
> Url: https://github.com/boostorg/fiber/issues/314  

Hello! We are trying to package the Boost 1.84.0 on Conan but we are facing a linkage error related to the new `boost::fibers::make_stack_allocator_wrapper` introduced on [1.84.0](https://github.com/boostorg/fiber/compare/boost-1.83.0...boost-1.84.0)  
  
We have the follow error when trying to link to the fiber example [work_sharing.cpp](https://github.com/boostorg/fiber/blob/develop/examples/work_sharing.cpp):  
  
```  
boost/1.84.0 (test package): RUN: cmake --build "C:\Users\uilia\Development\conan\conan-center-index\recipes\boost\all\test_package\build\msvc-193-x86_64-17-release" --config Release                                                                                                                                          
MSBuild version 17.3.1+2badb37d1 for .NET Framework                                                                                                                                                                                                                                                                             
  1>Checking Build System                                                                                                                                                                                                                                                                                                                                                                                                                                                   
  Building Custom Rule C:/Users/uilia/Development/conan/conan-center-index/recipes/boost/all/test_package/CMakeLists.txt                                                                                                                                                                                                        
  fiber.cpp                                                                                                                                                                                                                                                                                                                     
C:/Users/uilia/.conan2/p/b/boost9b0969d51319d/p/include\boost/fiber/stack_allocator_wrapper.hpp(85,43): error C2491: 'boost::fibers::make_stack_allocator_wrapper': definition of dllimport function not allowed [C:\Users\uilia\Development\conan\conan-center-index\recipes\boost\all\test_package\build\msvc-193-x86_64-17   
-release\fiber_exe.vcxproj]                                                                                                                                                                                                                                                                                                     
  Building Custom Rule C:/Users/uilia/Development/conan/conan-center-index/recipes/boost/all/test_package/CMakeLists.txt                                                                                                                                                                                                        
```  
  
In summary, we using the exactly same configuration for Boost 1.83.0 and it works perfectly with the same example file in this repository.   
  
All build and test logs are available on https://c3i.jfrog.io/c3i/misc/summary.html?json=https://c3i.jfrog.io/c3i/misc/logs/pr/21747/11-windows-visual_studio/boost/1.84.0//summary.json  
  
We use b2 to build Boost, including fiber. The CMake is only when consuming Boost, so we are not trying unofficial support.  
  
Some extra information about the generated fiber.dll:  
  
![Screenshot (93)](https://github.com/boostorg/fiber/assets/4870173/4ed02813-3a25-4368-a09c-5ecfb7485f9c)  
  
  
#### Environment  
  
Boost version: 1.84.0  
OS: Windows 10  
Compiler: MSVC 193  
Build Type: Release  
Runtime: Dynamic (MD)  
  
#### Steps to reproduce  
  
```  
git clone -b boost-1.84.0 https://github.com/toge/conan-center-index.git  
cd recipes/boost/all  
conan create . --version=1.84.0  
```  
  
OR, just build Boost as usual and try to link to work_sharing.cpp.

---

## Comment 1

> Username: xiaodaxia-2008  
> Created at: 2024-05-28 06:15:01 UTC  
> Url: https://github.com/boostorg/fiber/issues/314#issuecomment-2134421907  

I got this compile error too, then if I change line of code `stack_allocator_wrapper.hpp:85`  
```c++  
BOOST_FIBERS_DECL stack_allocator_wrapper make_stack_allocator_wrapper(Args && ... args)  
```  
to   
  
```c++  
stack_allocator_wrapper make_stack_allocator_wrapper(Args && ... args)  
```  
I could compile it successfully.  
  
It seems that `BOOST_FIBERS_DECL` is expanded as `__declspec(dllimport)` when used as shared library, but MSVC wouldn't allow `dllimport` on definition of a function. [see here](https://learn.microsoft.com/en-us/cpp/error-messages/compiler-errors-1/compiler-error-c2491?view=msvc-170)  
  
> 'identifier' : definition of dllimport function not allowed  
> Data, static data members, and functions can be declared as dllimports but not defined as dllimports.  
> To fix this issue, remove the __declspec(dllimport) specifier from the definition of the function.

---

## Comment 2

> Username: xiaodaxia-2008  
> Created at: 2024-06-07 08:19:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/314#issuecomment-2154335586  

I found that the following code will compile:  
```c++  
#define BOOST_FIBERS_STATIC_LINK  
#include <boost/fiber/all.hpp>  
```  
  
and in your cmake link Boost::context and Boost::fiber

---

## Comment 3

> Username: uilianries  
> Created at: 2024-06-07 09:03:54 UTC  
> Updated at: 2024-06-07 09:03:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/314#issuecomment-2154411319  

@xiaodaxia-2008 Thank you for sharing your solution! 😄

---

## Comment 4

> Username: xiaodaxia-2008  
> Created at: 2024-06-07 09:07:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/314#issuecomment-2154417660  

you are welcome! 😊
