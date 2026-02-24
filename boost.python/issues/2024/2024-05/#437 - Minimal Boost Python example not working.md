# #437 - Minimal Boost Python example not working [Open]

> Username: robertapplin  
> Created at: 2024-05-17 17:50:58 UTC  
> Updated at: 2024-06-07 05:54:43 UTC  
> Url: https://github.com/boostorg/python/issues/437  

I'm having trouble getting boost python to work with this minimal example. I've emailed the mailing list but have had no response.  
  
I'm using C++20  
  
```cpp  
#include <boost/python.hpp>  
  
#include <iostream>  
#include <string>  
  
void printStr(const std::string &testStr) {  
  std::cout << "BEFORE" <<std::endl;  
  std::cout << testStr <<std::endl;  
  std::cout << "AFTER" <<std::endl;  
}  
  
BOOST_PYTHON_MODULE(my_module) {  
  using namespace boost::python;  
  
  def("printStr", printStr);  
}  
```  
  
This compiles fine, but I get a segmentation fault when I attempt to call the function from python (using 3.10):  
  
```py  
import my_module  
  
message = "This is a test"  
  
my_module.printStr(message)  
```  
  
The output I get:  
  
```  
BEFORE  
Segmentation fault  
```  
  
Can you tell me what I'm doing wrong? Thanks in advance

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2024-05-17 19:00:45 UTC  
> Url: https://github.com/boostorg/python/issues/437#issuecomment-2118213770  

The code above looks fine. I suspect the problem stems from the way this is compiled. Can you share the details of your build & execution environment ? I.e., platform, compiler, build commands, etc.

---

## Comment 2

> Username: robertapplin  
> Created at: 2024-05-18 12:24:55 UTC  
> Updated at: 2024-05-18 12:26:28 UTC  
> Url: https://github.com/boostorg/python/issues/437#issuecomment-2118807622  

> The code above looks fine. I suspect the problem stems from the way this is compiled. Can you share the details of your build & execution environment ? I.e., platform, compiler, build commands, etc.  
  
Thanks for your quick reply!  
  
I'm on Windows 11, using the compiler shipped with Visual Studio 17 2022, toolset v142. Also I use Ninja as the generator. To compile from the build folder activate my conda environment and I just do  
```  
ninja  
```  
  
I get boost 1.84 from Conda-forge. My CMakeLists file that finds boost has:  
  
```  
find_package(Python REQUIRED COMPONENTS Development)   
 if(Python_FOUND)   
   include_directories(${Python_INCLUDE_DIRS})   
 else()   
   message(FATAL_ERROR "Python not found")   
 endif()   
   
find_package(Boost REQUIRED COMPONENTS python)   
 if(Boost_FOUND)   
   include_directories(${Boost_INCLUDE_DIRS})   
 else()   
   message(FATAL_ERROR "Boost not found")   
 endif()  
```  
  
I then link boost to the library that requires it. Perhaps I am missing a component of boost or python?

---

## Comment 3

> Username: robertapplin  
> Created at: 2024-05-18 12:28:14 UTC  
> Updated at: 2024-05-19 10:02:18 UTC  
> Url: https://github.com/boostorg/python/issues/437#issuecomment-2118808520  

My conda env is created using this:  
```  
name: dev-env  
  
channels:  
  - conda-forge  
  
dependencies:  
  - cmake  
  - boost  
  - ninja  
```

---

## Comment 4

> Username: robertapplin  
> Created at: 2024-05-19 10:01:51 UTC  
> Url: https://github.com/boostorg/python/issues/437#issuecomment-2119176826  

I've tried creating a minimal example here, but it seems to be struggling to build. Any ideas why? https://github.com/robertapplin/boost-example/pull/2

---

## Comment 5

> Username: MasterBe  
> Created at: 2024-05-26 15:42:17 UTC  
> Url: https://github.com/boostorg/python/issues/437#issuecomment-2132264786  

Not sure exact solution but can you try using something other than std::string e.g int, this will likely mean that your code does not find/accept converter for const std::string&. Also why not python 3.11..

---

## Comment 6

> Username: robertapplin  
> Created at: 2024-05-30 13:58:05 UTC  
> Url: https://github.com/boostorg/python/issues/437#issuecomment-2139619328  

It works for an `int`. The debugger seems to be able to read the std::string when I hover over the variable after setting a breakpoint. However it segfaults on the `std::cout` line  
  
I've also tried with python 3.10, and 3.11 - the same problem happens

---

## Comment 7

> Username: CEXT-Dan  
> Created at: 2024-06-07 05:54:41 UTC  
> Url: https://github.com/boostorg/python/issues/437#issuecomment-2154134389  

this is what I'm using to print  
https://github.com/CEXT-Dan/PyRx/blob/fd5b8ad47d3fddb50453277ba63ace1d1f51fb6f/PyRxCore/PyAcEd.cpp#L13-L18  
It's not to cout, and I don't remember why I locked the GIL, something for you to try
