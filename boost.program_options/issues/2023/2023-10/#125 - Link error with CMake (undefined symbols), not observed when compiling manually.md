# #125 - Link error with CMake (undefined symbols), not observed when compiling manually [Closed]

> Username: david-alvarez-rosa  
> Created at: 2023-10-14 16:53:36 UTC  
> Updated at: 2023-10-16 12:16:43 UTC  
> Closed at: 2023-10-16 12:16:43 UTC  
> Url: https://github.com/boostorg/program_options/issues/125  

I'm trying to link against `boost/program_options` library with a dummy program `main.cpp`  
  
```cpp  
#include <boost/program_options.hpp>  
  
int main() {  
  boost::program_options::options_description desc("My description");  
}  
```  
  
by using the following `CMakeLists.txt`  
  
```cmake  
cmake_minimum_required(VERSION 3.10)  
project(my-project)  
  
find_package(Boost 1.82 REQUIRED)  
add_executable(exe "main.cpp")  
target_include_directories(exe PRIVATE ${Boost_INCLUDE_DIR})  
target_link_libraries(exe ${Boost_LIBRARIES})  
```  
  
And I'm getting the following error  
  
```prompt  
$ cmake -B build  
...  [Succeeds]  
$ cmake --build build -v  
...  
[100%] Linking CXX executable exe  
/opt/homebrew/Cellar/cmake/3.27.6/bin/cmake -E cmake_link_script CMakeFiles/exe.dir/link.txt --verbose=1  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang++  -arch arm64 -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.0.sdk -mmacosx-version-min=13.6 -Wl,-search_paths_first -Wl,-headerpad_max_install_names CMakeFiles/exe.dir/main.cpp.o -o exe   
ld: Undefined symbols:  
  boost::program_options::options_description::m_default_line_length, referenced from:  
      _main in main.cpp.o  
  boost::program_options::options_description::options_description(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, unsigned int, unsigned int), referenced from:  
      _main in main.cpp.o  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
However, surprisingly, when compiling manually it works  
  
```prompt  
$ clang++ -I/opt/homebrew/include -L/opt/homebrew/lib -lboost_program_options main.cpp  
```  
  
I've been using Boost libraries in this same project, so I'm not sure what could be going wrong.   
  
Any pointers appreciated!

---

## Comment 1

> Username: david-alvarez-rosa  
> Created at: 2023-10-14 16:56:47 UTC  
> Url: https://github.com/boostorg/program_options/issues/125#issuecomment-1763039074  

I've Boost libraries installed (v. 1.82) directly by Homebrew  
  
```prompt  
$ brew install boost  
```

---

## Comment 2

> Username: david-alvarez-rosa  
> Created at: 2023-10-16 12:16:43 UTC  
> Url: https://github.com/boostorg/program_options/issues/125#issuecomment-1764349487  

Solved by   
  
```cmake  
find_package(Boost 1.65 COMPONENTS program_options REQUIRED)  
```
