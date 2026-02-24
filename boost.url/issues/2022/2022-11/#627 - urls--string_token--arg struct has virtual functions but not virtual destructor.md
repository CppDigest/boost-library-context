# #627 - urls::string_token::arg struct has virtual functions but not virtual destructor [Closed]

> Username: wilhelm-now  
> Created at: 2022-11-17 20:46:55 UTC  
> Updated at: 2022-11-18 05:18:35 UTC  
> Closed at: 2022-11-18 05:18:35 UTC  
> Url: https://github.com/boostorg/url/issues/627  

On MSVC 17, Using this library git tag `boost-1.81.0.beta1`, boost-1.80.0 (installed via conan) and this library (installed with CMake-FetchContent) I ran into a compiler error when I had elevated warning [C4265 Virtual class missing virtual destructor](https://learn.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-3-c4265?view=msvc-170) to error.   
  
main.cpp  
```  
#include <boost/url/url.hpp>  
  
int main()  
{  
  boost::urls::url url("http://boost.org");  
  return 0;  
}  
```  
  
CMakeLists.txt  
```  
CMAKE_MINIMUM_REQUIRED(VERSION 3.15)  
  
project(use_boost_url)  
  
find_package(Boost REQUIRED)  
include(FetchContent)  
FetchContent_Declare(boost_url_copy  
  GIT_REPOSITORY https://github.com/boostorg/url.git  
  GIT_TAG boost-1.81.0.beta1  
  GIT_SHALLOW 1  
  )  
FetchContent_MakeAvailable(boost_url_copy)  
  
add_library(demo main.cpp)  
target_link_libraries(demo Boost::url)  
  
# promote virtual class without virtual destructor to error  
# https://learn.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-3-c4265?view=msvc-170  
target_compile_options(demo PUBLIC /we4265)  
```  
conanfile.txt  
```  
[requires]  
boost/1.80.0  
  
[generators]  
CMakeDeps  
CMakeToolchain  
````  
  
conan install and cmake configure including some output to show compiler version  
```  
>mkdir build && cd build  
>conan install .. -s build_type=Release -s compiler="Visual Studio" -s compiler.version=17 -s arch=x86_64 --build=missing  
>cmake .. -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake   
-- Building for: Visual Studio 17 2022  
-- Using Conan toolchain: C:/Users/User/repos/use_boost_url/build/conan_toolchain.cmake  
-- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19043.  
-- The C compiler identification is MSVC 19.33.31630.0  
-- The CXX compiler identification is MSVC 19.33.31630.0  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.33.31629/bin/Hostx64/x64/cl.exe - skipped  
```  
  
Build output  
```  
\build>cmake --build . --config Release  
MSBuild version 17.3.1+2badb37d1 for .NET Framework  
  Checking File Globs  
  Checking Build System  
  Building Custom Rule C:/Users/User/repos/use_boost_url/build/_deps/boost_url_copy-src/CMakeLists.txt  
  src.cpp  
  boost_url.vcxproj -> C:\Users\User\repos\use_boost_url\build\_deps\boost_url_copy-build\Release\boost_url.lib  
  Building Custom Rule C:/Users/User/repos/use_boost_url/CMakeLists.txt  
  main.cpp  
C:\Users\User\repos\use_boost_url\build\_deps\boost_url_copy-src\include\boost/url/grammar/string_token.hpp(137,1): error C4265: 'boost::ur  
ls::string_token::return_string': class has virtual functions, but its non-trivial destructor is not virtual; instances of this class may n  
ot be destructed correctly [C:\Users\User\repos\use_boost_url\build\demo.vcxproj]  
```

---

## Comment 1

> Username: wilhelm-now  
> Created at: 2022-11-17 20:49:14 UTC  
> Url: https://github.com/boostorg/url/issues/627#issuecomment-1319183961  

Wrong library 🤦

---

## Comment 2

> Username: pdimov  
> Created at: 2022-11-17 20:50:02 UTC  
> Url: https://github.com/boostorg/url/issues/627#issuecomment-1319184168  

Moved.

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-11-17 21:03:10 UTC  
> Url: https://github.com/boostorg/url/issues/627#issuecomment-1319197935  

Should probably have protected destructor.
