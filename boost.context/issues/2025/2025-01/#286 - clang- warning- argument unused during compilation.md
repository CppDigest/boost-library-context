# #286 - [build] clang: warning: argument unused during compilation: ... [`-Wunused-command-line-argument`] [Closed]

> Username: Challanger524  
> Created at: 2025-01-29 11:31:00 UTC  
> Updated at: 2025-02-21 17:29:30 UTC  
> Closed at: 2025-02-21 17:29:30 UTC  
> Url: https://github.com/boostorg/context/issues/286  

Similar to https://github.com/boostorg/context/issues/287  
An odd Clang warning print disrupts build output.  
  
``` cmake  
# ATTENTION. Some assemblers are picky to get --defsym,KEY=VALUE pairs where '=' is non-optional!  
# Be sure all defines have the '=' character. Overkill solution: always put '=' throughout this module.  
```  
  
But I am not sure about `'-MD'` , ` '-MT dep/..'`, `'-MF dep\..'`, justifiable to ignore this too?  
  
  
CMake ninja build log snippet:  
```  
...  
[build] [7/19] Building ASM object dep/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_ms_pe_gas.asm.obj  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_EXPORT=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_NO_LIB=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_SOURCE=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_STATIC_LINK=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MD' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MT dep/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_ms_pe_gas.asm.obj' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MF dep\boost\libs\context\CMakeFiles\boost_context.dir\src\asm\make_x86_64_ms_pe_gas.asm.obj.d' [-Wunused-command-line-argument]  
...  
```  
  
<details><summary>Bigger log:</summary>  
  
```  
...  
[cmake] -- Build files have been written to: C:/*/dev/test-boost-beast/out/build/clang-dbg  
[build] Starting build  
[proc] Executing command: "C:\Program Files\CMake\bin\cmake.EXE" --build C:/*/dev/test-boost-beast/out/build/clang-dbg --parallel 4 --  
[build] [0/2] Re-checking globbed directories...  
[build] [1/19] Building CXX object dep/boost/libs/container/CMakeFiles/boost_container.dir/src/dlmalloc.cpp.obj  
[build] [2/19] Building CXX object CMakeFiles/test-beast.dir/cmake_pch.hxx.pch  
[build] [3/19] Building CXX object dep/boost/libs/container/CMakeFiles/boost_container.dir/src/monotonic_buffer_resource.cpp.obj  
[build] [4/19] Building CXX object dep/boost/libs/container/CMakeFiles/boost_container.dir/src/global_resource.cpp.obj  
[build] [5/19] Building CXX object dep/boost/libs/container/CMakeFiles/boost_container.dir/src/synchronized_pool_resource.cpp.obj  
[build] [6/19] Building CXX object dep/boost/libs/container/CMakeFiles/boost_container.dir/src/pool_resource.cpp.obj  
[build] [7/19] Building ASM object dep/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_ms_pe_gas.asm.obj  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_EXPORT=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_NO_LIB=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_SOURCE=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_STATIC_LINK=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MD' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MT dep/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_ms_pe_gas.asm.obj' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MF dep\boost\libs\context\CMakeFiles\boost_context.dir\src\asm\make_x86_64_ms_pe_gas.asm.obj.d' [-Wunused-command-line-argument]  
[build] [8/19] Building ASM object dep/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/jump_x86_64_ms_pe_gas.asm.obj  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_EXPORT=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_NO_LIB=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_SOURCE=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_STATIC_LINK=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MD' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MT dep/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/jump_x86_64_ms_pe_gas.asm.obj' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MF dep\boost\libs\context\CMakeFiles\boost_context.dir\src\asm\jump_x86_64_ms_pe_gas.asm.obj.d' [-Wunused-command-line-argument]  
[build] [9/19] Building ASM object dep/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/ontop_x86_64_ms_pe_gas.asm.obj  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_EXPORT=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_NO_LIB=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_SOURCE=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-D BOOST_CONTEXT_STATIC_LINK=' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MD' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MT dep/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/ontop_x86_64_ms_pe_gas.asm.obj' [-Wunused-command-line-argument]  
[build] clang: warning: argument unused during compilation: '-MF dep\boost\libs\context\CMakeFiles\boost_context.dir\src\asm\ontop_x86_64_ms_pe_gas.asm.obj.d' [-Wunused-command-line-argument]  
[build] [10/19] Building CXX object dep/boost/libs/container/CMakeFiles/boost_container.dir/src/unsynchronized_pool_resource.cpp.obj  
[build] [11/19] Building C object dep/boost/libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.obj  
[build] [12/19] Building CXX object dep/boost/libs/context/CMakeFiles/boost_context.dir/src/fcontext.cpp.obj  
[build] [13/19] Building CXX object dep/boost/libs/date_time/CMakeFiles/boost_date_time.dir/src/gregorian/greg_month.cpp.obj  
[build] [14/19] Linking CXX static library dep\boost\libs\container\libboost_container-clang19-mt-d-x64-1_88.a  
[build] [15/19] Linking CXX static library dep\boost\libs\date_time\libboost_date_time-clang19-mt-d-x64-1_88.a  
[build] [16/19] Building CXX object dep/boost/libs/context/CMakeFiles/boost_context.dir/src/windows/stack_traits.cpp.obj  
[build] [17/19] Linking CXX static library dep\boost\libs\context\libboost_context-clang19-mt-d-x64-1_88.a  
[build] [18/19] Building CXX object CMakeFiles/test-beast.dir/src/main.cpp.obj  
[build] [19/19] Linking CXX executable C:\*\dev\test-boost-beast\test-beast.exe  
[driver] Build completed: 00:00:13.909  
[build] Build finished with exit code 0  
```  
</details>  
  
## Solution  
  
``` cmake  
if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")  
  set_property(SOURCE ${ASM_SOURCES} APPEND PROPERTY COMPILE_OPTIONS "-Wno-unused-command-line-argument")  
endif()  
```  
  
### Previous imperfect solution  
  
Working solution is to add `-Wno-unused-command-line-argument` to **Clang** in `context/CMakeLists.txt`:  
``` cmake  
if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")  
   target_compile_options(${PROJECT_NAME} PRIVATE "-Wno-unused-command-line-argument")  
endif()  
```
