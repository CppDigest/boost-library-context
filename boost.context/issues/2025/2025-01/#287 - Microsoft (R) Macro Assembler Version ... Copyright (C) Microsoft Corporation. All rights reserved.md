# #287 - [build] Microsoft (R) Macro Assembler Version ... Copyright (C) Microsoft Corporation.  All rights reserved. [Closed]

> Username: Challanger524  
> Created at: 2025-01-29 11:36:54 UTC  
> Updated at: 2025-02-21 17:30:24 UTC  
> Closed at: 2025-02-21 17:30:24 UTC  
> Url: https://github.com/boostorg/context/issues/287  

Similar to https://github.com/boostorg/context/issues/286  
Less of an issue, but an odd microsoft assembler `ML` logo print disrupts build output.  
  
CMake ninja build log snippet:  
```  
...  
[build] [6/19] Building CXX object dep\boost\libs\container\CMakeFiles\boost_container.dir\src\pool_resource.cpp.obj  
[build] [7/19] Building ASM_MASM object dep\boost\libs\context\CMakeFiles\boost_context.dir\src\asm\make_i386_ms_pe_masm.asm.obj  
[build] Microsoft (R) Macro Assembler Version 14.39.33523.0  
[build] Copyright (C) Microsoft Corporation.  All rights reserved.  
[build]   
[build]  Assembling: C:\*\dev\boost\libs\context\src\asm\make_i386_ms_pe_masm.asm  
...  
```  
  
<details><summary>Bigger log:</summary>  
  
```  
[build] Starting build  
[proc] Executing command: "C:\Program Files\CMake\bin\cmake.EXE" --build C:/*/dev/test-boost-beast/out/build/msvc-dbg --parallel 4 --  
[build] [0/2] Re-checking globbed directories...  
[build] [1/19] Building CXX object dep\boost\libs\container\CMakeFiles\boost_container.dir\src\dlmalloc.cpp.obj  
[build] [2/19] Building CXX object dep\boost\libs\container\CMakeFiles\boost_container.dir\src\global_resource.cpp.obj  
[build] [3/19] Building C object dep\boost\libs\container\CMakeFiles\boost_container.dir\src\alloc_lib.c.obj  
[build] [4/19] Building CXX object CMakeFiles\test-beast.dir\cmake_pch.cxx.obj  
[build] [5/19] Building CXX object dep\boost\libs\container\CMakeFiles\boost_container.dir\src\monotonic_buffer_resource.cpp.obj  
[build] [6/19] Building CXX object dep\boost\libs\container\CMakeFiles\boost_container.dir\src\pool_resource.cpp.obj  
[build] [7/19] Building ASM_MASM object dep\boost\libs\context\CMakeFiles\boost_context.dir\src\asm\make_i386_ms_pe_masm.asm.obj  
[build] Microsoft (R) Macro Assembler Version 14.39.33523.0  
[build] Copyright (C) Microsoft Corporation.  All rights reserved.  
[build]   
[build]  Assembling: C:\*\dev\boost\libs\context\src\asm\make_i386_ms_pe_masm.asm  
[build] [8/19] Building ASM_MASM object dep\boost\libs\context\CMakeFiles\boost_context.dir\src\asm\jump_i386_ms_pe_masm.asm.obj  
[build] Microsoft (R) Macro Assembler Version 14.39.33523.0  
[build] Copyright (C) Microsoft Corporation.  All rights reserved.  
[build]   
[build]  Assembling: C:\*\dev\boost\libs\context\src\asm\jump_i386_ms_pe_masm.asm  
[build] [9/19] Building CXX object dep\boost\libs\container\CMakeFiles\boost_container.dir\src\synchronized_pool_resource.cpp.obj  
[build] [10/19] Building ASM_MASM object dep\boost\libs\context\CMakeFiles\boost_context.dir\src\asm\ontop_i386_ms_pe_masm.asm.obj  
[build] Microsoft (R) Macro Assembler Version 14.39.33523.0  
[build] Copyright (C) Microsoft Corporation.  All rights reserved.  
[build]   
[build]  Assembling: C:\*\dev\boost\libs\context\src\asm\ontop_i386_ms_pe_masm.asm  
[build] [11/19] Building CXX object dep\boost\libs\container\CMakeFiles\boost_container.dir\src\unsynchronized_pool_resource.cpp.obj  
[build] [12/19] Building CXX object dep\boost\libs\context\CMakeFiles\boost_context.dir\src\fcontext.cpp.obj  
[build] [13/19] Linking CXX static library dep\boost\libs\container\libboost_container-vc143-mt-gd-x32-1_88.lib  
[build] [14/19] Building CXX object dep\boost\libs\date_time\CMakeFiles\boost_date_time.dir\src\gregorian\greg_month.cpp.obj  
[build] [15/19] Linking CXX static library dep\boost\libs\date_time\libboost_date_time-vc143-mt-gd-x32-1_88.lib  
[build] [16/19] Building CXX object dep\boost\libs\context\CMakeFiles\boost_context.dir\src\windows\stack_traits.cpp.obj  
[build] [17/19] Linking CXX static library dep\boost\libs\context\libboost_context-vc143-mt-gd-x32-1_88.lib  
[build] [18/19] Building CXX object CMakeFiles\test-beast.dir\src\main.cpp.obj  
[build] [19/19] Linking CXX executable C:\*\dev\test-boost-beast\test-beast.exe  
[driver] Build completed: 00:00:15.436  
[build] Build finished with exit code 0  
```  
</details>  
  
## Solution  
  
``` cmake  
if(BOOST_CONTEXT_ASSEMBLER STREQUAL masm)  
    set_property(SOURCE ${ASM_SOURCES} APPEND PROPERTY COMPILE_OPTIONS "/nologo")  
endif()  
```  
  
<details><summary>Previous imperfect solutions:</summary>  
  
Working solution is to add `/nologo` to `ML` (**MSVC** assembler) in `context/CMakeLists.txt`:  
``` cmake  
if(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")  
   target_compile_options(${PROJECT_NAME} PRIVATE "/nologo")  
endif()  
```  
Not sure how properly to pass assembler flags properly.  
CMake 3.10.3 https://cmake.org/cmake/help/v3.10/envvar/ASM_DIALECT.html  
CMake 3.10.3 https://cmake.org/cmake/help/latest/envvar/ASM_DIALECTFLAGS.html  
  
Another way is to:  
``` cmake  
if(BOOST_CONTEXT_ASSEMBLER STREQUAL "masm")  
  set(CMAKE_ASM_MASM_FLAGS "/nologo" CACHE STRING "" FORCE) # not setting without force  
endif()  
```  
but it fully overwrites `CMAKE_ASM_MASM_FLAGS`, setting with appending via `set(CMAKE_ASM_MASM_FLAGS ${CMAKE_ASM_MASM_FLAGS} "/nologo" CACHE STRING "" FORCE)` will append the flag on each CMake configure - not an option.  
</details>
