# #34 - Test if import std; is possible is wrong implemented [Closed]

> Username: ClausKlein  
> Created at: 2025-12-01 17:29:14 UTC  
> Updated at: 2026-01-01 07:58:27 UTC  
> Closed at: 2026-01-01 07:55:50 UTC  
> Url: https://github.com/boostorg/any/issues/34  

Only `cmake -D CMAKE_CXX_MODULE_STD=ON` the std module may be build if possible!  
  
```bash  
bash-5.3$ make build  
cmake -S . -B build -G Ninja -D CMAKE_SKIP_INSTALL_RULES=ON \  
		-D CMAKE_EXPERIMENTAL_CXX_IMPORT_STD="d0edc3af-4c50-42ea-a356-e2862fe7a444" \  
		-D CMAKE_CXX_STANDARD=23 -D CMAKE_CXX_EXTENSIONS=ON -D CMAKE_CXX_STANDARD_REQUIRED=ON \  
		-D BUILD_TESTING=ON -D BOOST_INCLUDE_LIBRARIES='any;type_index;test' \  
		-D CMAKE_CXX_STDLIB_MODULES_JSON=/usr/local/Cellar/llvm/21.1.6/lib/c++/libc++.modules.json \  
		-D BOOST_USE_MODULES=ON #XXX -D CMAKE_CXX_MODULE_STD=OFF \  
		# XXX --fresh  
-- The CXX compiler identification is Clang 21.1.6  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/local/Cellar/llvm/21.1.6/bin/clang++ - skipped  
-- Detecting CXX compile features  
CMake Warning (dev) at /Users/clausklein/.local/share/cmake-4.2/Modules/Compiler/CMakeCommonCompilerMacros.cmake:248 (cmake_language):  
  CMake's support for `import std;` in C++23 and newer is experimental.  It  
  is meant only for experimentation and feedback to CMake developers.  
Call Stack (most recent call first):  
  /Users/clausklein/.local/share/cmake-4.2/Modules/CMakeDetermineCompilerSupport.cmake:113 (cmake_create_cxx_import_std)  
  /Users/clausklein/.local/share/cmake-4.2/Modules/CMakeTestCXXCompiler.cmake:83 (CMAKE_DETERMINE_COMPILER_SUPPORT)  
  CMakeLists.txt:20 (project)  
This warning is for project developers.  Use -Wno-dev to suppress it.  
  
-- Detecting CXX compile features - done  
-- CMAKE_CXX_COMPILER_IMPORT_STD=23;26  
-- CMAKE_CXX_STANDARD=23  
-- CMAKE_CXX_MODULE_STD=  
-- CMAKE_CXX_SCAN_FOR_MODULES=ON  
-- Boost: using system layout: include, bin, lib, lib/cmake, share  
-- Boost: using CMake 4.2.0-gd4539f6  
-- Boost: Release build, static libraries, MPI OFF, Python OFF, testing ON  
-- Boost: libraries included: any;type_index;test  
-- Using `import std;`  
-- Using `import std;`  
-- Configuring done (1.7s)  
-- Generating done (0.3s)  
-- Build files have been written to: /Users/clausklein/Workspace/cpp/boost-git/build  
bash-5.3$ make  
ln -sf build/compile_commands.json .  
ninja -C build  
ninja: Entering directory `build'  
[0/2] Re-checking globbed directories...  
[107/123] Building CXX object libs/type_index/CMakeFiles/boost_type_index.dir/modules/boost_type_index.cppm.o  
FAILED: [code=1] libs/type_index/CMakeFiles/boost_type_index.dir/modules/boost_type_index.cppm.o libs/type_index/CMakeFiles/boost_type_index.dir/boost.type_index.pcm   
ccache /usr/local/Cellar/llvm/21.1.6/bin/clang++ -DBOOST_TYPE_INDEX_USE_STD_MODULE -DBOOST_USE_MODULES -I/Users/clausklein/Workspace/cpp/boost-git/libs/type_index/include -I/Users/clausklein/Workspace/cpp/boost-git/libs/config/include -I/Users/clausklein/Workspace/cpp/boost-git/libs/container_hash/include -I/Users/clausklein/Workspace/cpp/boost-git/libs/describe/include -I/Users/clausklein/Workspace/cpp/boost-git/libs/mp11/include -I/Users/clausklein/Workspace/cpp/boost-git/libs/throw_exception/include -I/Users/clausklein/Workspace/cpp/boost-git/libs/assert/include -O3 -DNDEBUG -std=gnu++23 -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/type_index/CMakeFiles/boost_type_index.dir/modules/boost_type_index.cppm.o -MF libs/type_index/CMakeFiles/boost_type_index.dir/modules/boost_type_index.cppm.o.d @libs/type_index/CMakeFiles/boost_type_index.dir/modules/boost_type_index.cppm.o.modmap -o libs/type_index/CMakeFiles/boost_type_index.dir/modules/boost_type_index.cppm.o -c /Users/clausklein/Workspace/cpp/boost-git/libs/type_index/modules/boost_type_index.cppm  
/Users/clausklein/Workspace/cpp/boost-git/libs/type_index/modules/boost_type_index.cppm:42:8: fatal error: module 'std' not found  
   42 | import std;  
      | ~~~~~~~^~~  
1 error generated.  
ninja: build stopped: subcommand failed.  
make: *** [all] Error 1  
bash-5.3$   
```

---

## Comment 1

> Username: ClausKlein  
> Created at: 2025-12-01 17:30:30 UTC  
> Url: https://github.com/boostorg/any/issues/34#issuecomment-3597900443  

This would be right:  
```cmake  
    if(${CMAKE_CXX_STANDARD} IN_LIST CMAKE_CXX_COMPILER_IMPORT_STD)  
        set(CMAKE_CXX_MODULE_STD ON)  
        target_compile_definitions(boost_any PRIVATE BOOST_ANY_USE_STD_MODULE)  
        message(STATUS "Using `import std;`")  
    else()  
        message(WARNING "`import std;` is not available")  
    endif()  
```

---

## Comment 2

> Username: apolukhin  
> Created at: 2025-12-31 13:17:06 UTC  
> Url: https://github.com/boostorg/any/issues/34#issuecomment-3702196958  

Many thanks for the report and for the fix ideas in https://github.com/boostorg/any/pull/35  
  
I'll apply the fix from https://github.com/boostorg/any/pull/37 soon, please check that it fixes the issue on your environment.

---

## Comment 3

> Username: apolukhin  
> Created at: 2026-01-01 07:58:27 UTC  
> Url: https://github.com/boostorg/any/issues/34#issuecomment-3703387588  

@ClausKlein merged the fix into the develop branch in https://github.com/boostorg/any/commit/58651618becc74707bcaf09f277e1cfaa69f4bd7  
  
Please check that the fix works for you
