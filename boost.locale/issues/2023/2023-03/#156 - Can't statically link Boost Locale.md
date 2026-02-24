# #156 - Can't statically link Boost Locale [Closed]

> Username: Gab-Menezes  
> Created at: 2023-03-10 03:22:29 UTC  
> Updated at: 2025-01-12 16:40:13 UTC  
> Closed at: 2025-01-12 16:40:13 UTC  
> Url: https://github.com/boostorg/locale/issues/156  

Trying to statically link Boost Locale with cmake returns an error.  
  
CMakeLists.txt  
```cmake  
cmake_minimum_required(VERSION 3.16)  
project(main)  
  
#set(Boost_USE_STATIC_LIBS ON)  
find_package(Boost COMPONENTS locale REQUIRED)  
if(NOT Boost_FOUND)  
    message(FATAL_ERROR "Some Boost libraries missing in the system")  
endif()  
  
  
add_executable(main main.cpp)  
target_link_libraries(main ${Boost_LIBRARIES})  
````  
  
main.cpp  
```c++  
#include <iostream>  
#include <locale>  
#include <boost/locale.hpp>  
  
int main() {  
    boost::locale::generator gen;  
    std::locale loc1 = gen("pt_BR.UTF-8");  
    return 0;  
}  
```  
Dynamic linkage works fine but uncommenting `set(Boost_USE_STATIC_LIBS ON)` will result in an error.  
The boost library was installed via package manager with `sudo apt install libboost-all-dev`.   
  
As we can see in the prints I have Locale installed and also the icu stuff.  
![image](https://user-images.githubusercontent.com/29588635/224214616-37228e15-1e47-4bdb-abd7-e238f28343cb.png)  
![image](https://user-images.githubusercontent.com/29588635/224214702-b0c834bc-34e8-452b-a1d3-752e1d1ea843.png)  
  
`ldd` of the binary if not statically linked.  
![image](https://user-images.githubusercontent.com/29588635/224214931-d81a2914-e105-4c95-9aac-c81f8eabd889.png)  
  
I can statically link other boost libraries, but locale in specific gives me an error.  
  
Output of the `make` command: [log.txt](https://github.com/boostorg/locale/files/10938141/log.txt)  
  
Ubuntu version: `20.04` and `22.04`  
Cmake version: >= `3.16`  
Boost version: `1.71` and `1.74`  
g++ version: `10.3` and `11.3`

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-03-11 10:33:08 UTC  
> Url: https://github.com/boostorg/locale/issues/156#issuecomment-1464881286  

As the errors are all of "undefined reference to <ICU name>" you need to link to the ICU target. In particular icudata, icui18n, icuuc, e.g. by using [`find_package(ICU ...)`](https://cmake.org/cmake/help/latest/module/FindICU.html) and the targets `ICU:data ICU::i18n ICU::uc`  
  
This is done automatically for shared libraries in general. Boost 1.75 config files add those dependencies too in all cases. So upgrading to Boost 1.75 or newer will also solve this.  
  
Side note:  
```  
find_package(Boost COMPONENTS locale REQUIRED)  
  
# This is redundant due to the above REQUIRED   
if(NOT Boost_FOUND)  
    message(FATAL_ERROR "Some Boost libraries missing in the system")  
endif()  
```
