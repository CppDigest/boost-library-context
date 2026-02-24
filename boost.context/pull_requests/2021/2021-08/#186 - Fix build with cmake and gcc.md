# #186 Fix build with cmake and gcc [Merged]

> Username: yh-sb  
> Created at: 2021-08-22 11:05:40 UTC  
> Updated at: 2021-08-22 19:01:15 UTC  
> Merged at: 2021-08-22 19:01:09 UTC  
> Closed at: 2021-08-22 19:01:09 UTC  
> Url: https://github.com/boostorg/context/pull/186  

Without this, asm files passed to gcc with "-c" option (without preprocessing).  
It leads to the following warnings and error:  
```log  
[  7%] Building ASM object libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_ms_pe_gas.asm.obj  
gcc.exe: warning: C:\boost\libs\context\src\asm\make_x86_64_ms_pe_gas.asm: linker input file unused because linking not done  
[  7%] Building ASM object libs/context/CMakeFiles/boost_context.dir/src/asm/jump_x86_64_ms_pe_gas.asm.obj  
gcc.exe: warning: C:\boost\libs\context\src\asm\jump_x86_64_ms_pe_gas.asm: linker input file unused because linking not done  
[  8%] Building ASM object libs/context/CMakeFiles/boost_context.dir/src/asm/ontop_x86_64_ms_pe_gas.asm.obj  
gcc.exe: warning: C:\boost\libs\context\src\asm\ontop_x86_64_ms_pe_gas.asm: linker input file unused because linking not done  
[  8%] Building CXX object libs/context/CMakeFiles/boost_context.dir/src/windows/stack_traits.cpp.obj  
[  8%] Linking CXX static library ..\..\stage\lib\libboost_context-gcc11-mt-x64-1_78.a  
C:\mydevtools\mingw64\bin\ar.exe: CMakeFiles/boost_context.dir/src/asm/make_x86_64_ms_pe_gas.asm.obj: No such file or directory  
mingw32-make[2]: *** [libs\context\CMakeFiles\boost_context.dir\build.make:146: stage/lib/libboost_context-gcc11-mt-x64-1_78.a] Error 1  
```  
  
This merge request fixes it.

---

## Comment 1

> Username: olk  
> Created_at: 2021-08-22 19:01:15 UTC  
> Url: https://github.com/boostorg/context/pull/186#issuecomment-903315210  

ty

---
