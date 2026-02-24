# #637 - add_executable called with incorrect number of arguments in CMakeLists.txt [Closed]

> Username: cloudlakecho  
> Created at: 2022-03-05 00:45:50 UTC  
> Updated at: 2022-03-07 20:01:52 UTC  
> Closed at: 2022-03-07 11:48:29 UTC  
> Url: https://github.com/boostorg/gil/issues/637  

Thanks for reading my issue during installation.   
  
I found the Boost Gil tool recently and tried to use it (to escape from the OpenCV tool ;) ).  
  
So I attempted to build Gil (by cloning the repository master branch) with these command lines:  
```  
cd gil  
mkdir build  
cd build  
cmake .. -DCMAKE_BUILD_TYPE=Release  
```  
  
### Actual behavior  
  
but I face these errors:   
  
output    
```  
~/Downloads/gil/build$ cmake .. -DCMAKE_BUILD_TYPE=Release  
-- Boost.GIL: Require C++11  
-- Boost.GIL: Turning on detailed compiler warnings  
-- Boost.GIL: Looking for Boost from current source tree and libraries from stage.  
-- Boost.GIL: Disable stage look-up with passing -DBOOST_ROOT=/path/to/your/boost.  
-- Boost 1.72.0 found.  
-- Found Boost components:  
   filesystem  
-- Boost.GIL: Using Boost_INCLUDE_DIRS=/usr/include  
-- Boost.GIL: Using Boost_LIBRARY_DIRS=  
-- Conan: Automatic detection of conan settings from cmake  
-- Conan: Settings= -s;build_type=Release;-s;compiler=gcc;-s;compiler.version=7;-s;compiler.libcxx=libstdc++11  
-- Conan: checking conan executable  
-- Conan: Found program /home/ccho/anaconda3/bin/conan  
-- Conan: Version found Conan version 1.45.0  
  
-- Conan executing: /home/ccho/anaconda3/bin/conan install /home/ccho/Downloads/gil/conanfile.txt -s build_type=Release -s compiler=gcc -s compiler.version=7 -s compiler.libcxx=libstdc++11 -g=cmake --build=missing  
Configuration:  
[settings]  
arch=x86_64  
arch_build=x86_64  
build_type=Release  
compiler=gcc  
compiler.libcxx=libstdc++11  
compiler.version=7  
os=Linux  
os_build=Linux  
[options]  
[build_requires]  
[env]  
.  
.  
.  
-- Boost.GIL: Configuring tests in test/extension/io  
-- Boost.GIL: Configuring self-contained header tests for all headers  
CMake Error at test/header/CMakeLists.txt:63 (add_executable):  
  add_executable called with incorrect number of arguments  
  
  
CMake Error at test/header/CMakeLists.txt:65 (target_sources):  
  Cannot specify sources for target "test_header_concepts-basic" which is not  
  built by this project.  
  
  
CMake Error at test/header/CMakeLists.txt:71 (target_link_libraries):  
  Cannot specify link libraries for target "test_header_concepts-basic" which  
  is not built by this project.  
  
  
-- Configuring incomplete, errors occurred!  
See also "/home/ccho/Downloads/gil/build/CMakeFiles/CMakeOutput.log".  
  
```  
### Expected  behavior  
  
Build Success  
  
### C++ Minimal Working Example  
  
None because this is from installation.   
  
### Environment  
  
- Compiler version: gcc (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0  
- Build settings:  
```  
option(BOOST_GIL_BUILD_EXAMPLES "Build examples" ON)  
option(BOOST_GIL_BUILD_HEADER_TESTS "Enable self-contained header tests" ON)  
option(BOOST_GIL_ENABLE_EXT_DYNAMIC_IMAGE "Enable Dynamic Image extension, test\  
s and examples" ON)  
option(BOOST_GIL_ENABLE_EXT_IO "Enable IO extension, tests and examples (requir\  
e libjpeg, libpng, libtiff)" ON)  
option(BOOST_GIL_ENABLE_EXT_NUMERIC "Enable Numeric extension, tests and exampl\  
es" ON)  
option(BOOST_GIL_ENABLE_EXT_TOOLBOX "Enable Toolbox extension, tests and exampl\  
es" ON)  
option(BOOST_GIL_USE_CONAN "Use Conan to install dependencies" ON)  
option(BOOST_GIL_USE_CLANG_TIDY "Set CMAKE_CXX_CLANG_TIDY property on targets t\  
o enable clang-tidy linting" ON)  
set(CMAKE_CXX_STANDARD 11 CACHE STRING "C++ standard version to use (default is\  
 11)")  
```  
- Version (Git ref or `<boost/version.hpp>`): Boost 1.72.0 (I actually installed 1.78 but 1.72.0 showing from `cmake ..`)  
- OS: Ubuntu 18.04  
  
It looks like I miss something. Please let me know, thanks.

---

## Comment 1

> Username: mloskot  
> Created at: 2022-03-07 11:48:26 UTC  
> Url: https://github.com/boostorg/gil/issues/637#issuecomment-1060603410  

> So I attempted to build Gil   
  
From the `README.md`: _"The Boost Generic Image Library (GIL) is a C++11 header-only library"_,  
  
As the `README.md` points out, CMake is dedicated for contributors only. If you wish to contribute, you need to read the warning(s) and instructions in the `CONTRIBUTING.md`.

---

## Comment 2

> Username: cloudlakecho  
> Created at: 2022-03-07 17:28:52 UTC  
> Url: https://github.com/boostorg/gil/issues/637#issuecomment-1060940417  

@mloskot Thanks for indication in the Read Me file.   
I actually read it but I still thought I had to compile the Boost Git for using it.   
  
To be clarify "the head only" meaning we don't have to link against binaries because the whole code of the library is contained in headers. The code will be compiled when we include them in the project. Is correct?

---

## Comment 3

> Username: lpranam  
> Created at: 2022-03-07 17:35:26 UTC  
> Url: https://github.com/boostorg/gil/issues/637#issuecomment-1060946273  

@cloudlakecho yes

---

## Comment 4

> Username: mloskot  
> Created at: 2022-03-07 20:01:52 UTC  
> Url: https://github.com/boostorg/gil/issues/637#issuecomment-1061080522  

@cloudlakecho https://www.boost.org/doc/libs/1_78_0/more/getting_started/windows.html#header-only-libraries
