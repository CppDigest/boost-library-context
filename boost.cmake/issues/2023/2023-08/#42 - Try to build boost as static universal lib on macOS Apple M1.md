# #42 - Try to build boost as static universal lib on macOS Apple M1 [Open]

> Username: diuming  
> Created at: 2023-08-14 10:05:01 UTC  
> Updated at: 2023-08-19 23:06:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/42  

Try to build as `universal` lib on macOS Apple M1, but always failed.  
Is it possible to build boost as `universal` lib on macOS Apple M1?  
  
  
cmake command with options  
---  
```  
  cmake \  
  -DCMAKE_INSTALL_PREFIX=/mylib/universal/boost-1.83.0 \  
  -DCMAKE_OSX_DEPLOYMENT_TARGET=11.0 \  
  -DCMAKE_OSX_ARCHITECTURES="arm64;x86_64" \  
  -DCMAKE_BUILD_TYPE=Release \  
  -DBUILD_SHARED_LIBS=OFF \  
  -DZLIB_ROOT=/mylib/universal/zlib-1.2.13 \  
  -DBZip2_ROOT=/mylib/universal/bzip2-1.0.8 \  
  -DIconv_ROOT=/mylib/universal/libiconv-1.17 \  
  -DBOOST_ENABLE_PYTHON=OFF \  
  -S . \  
  -B _build  
```  
  
Error messages  
---  
```  
[  0%] Building CXX object libs/atomic/CMakeFiles/boost_atomic.dir/src/lock_pool.cpp.o  
[  0%] Linking CXX static library ../../stage/lib/libboost_atomic.a  
[  0%] Built target boost_atomic  
[  0%] Building CXX object libs/chrono/CMakeFiles/boost_chrono.dir/src/chrono.cpp.o  
[  1%] Building CXX object libs/chrono/CMakeFiles/boost_chrono.dir/src/process_cpu_clocks.cpp.o  
[  1%] Building CXX object libs/chrono/CMakeFiles/boost_chrono.dir/src/thread_clock.cpp.o  
[  1%] Linking CXX static library ../../stage/lib/libboost_chrono.a  
[  1%] Built target boost_chrono  
[  1%] Building C object libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o  
[  2%] Building CXX object libs/container/CMakeFiles/boost_container.dir/src/dlmalloc.cpp.o  
[  2%] Building CXX object libs/container/CMakeFiles/boost_container.dir/src/global_resource.cpp.o  
[  2%] Building CXX object libs/container/CMakeFiles/boost_container.dir/src/monotonic_buffer_resource.cpp.o  
[  3%] Building CXX object libs/container/CMakeFiles/boost_container.dir/src/pool_resource.cpp.o  
[  3%] Building CXX object libs/container/CMakeFiles/boost_container.dir/src/synchronized_pool_resource.cpp.o  
[  3%] Building CXX object libs/container/CMakeFiles/boost_container.dir/src/unsynchronized_pool_resource.cpp.o  
[  3%] Linking CXX static library ../../stage/lib/libboost_container.a  
[  3%] Built target boost_container  
[  4%] Building ASM object libs/context/CMakeFiles/boost_context.dir/src/asm/make_arm64_aapcs_macho_gas.S.o  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:59:21: error: unexpected token in argument list  
    ; shift address in x0 (allocated stack) to lower 16 byte boundary  
                    ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:60:5: error: unknown use of instruction mnemonic without a size suffix  
    and x0, x0, ~0xF  
    ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:62:21: error: unexpected token in argument list  
    ; reserve space for context-data on context-stack  
                    ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:63:17: error: unknown token in expression  
    sub x0, x0, #0xb0  
                ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:65:17: error: unexpected token in argument list  
    ; third arg of make_fcontext() == address of context-function  
                ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:66:21: error: unexpected token in argument list  
    ; store address as a PC to jump in  
                    ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:67:13: error: brackets expression not supported on this target  
    str x2, [x0, #0xa0]  
            ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:69:5: error: invalid instruction mnemonic 'adr'  
    adr x1, finish  
    ^~~  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:71:20: error: unexpected token in argument list  
    ; save address of finish as return-address for context-function  
                   ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:72:15: error: unexpected token in argument list  
    ; will be entered after context-function returns (LR register)  
              ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:73:13: error: brackets expression not supported on this target  
    str x1, [x0, #0x98]  
            ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:75:5: error: unknown use of instruction mnemonic without a size suffix  
    ret lr ; return pointer to context-data (x0)  
    ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:75:29: error: unexpected token in argument list  
    ret lr ; return pointer to context-data (x0)  
                            ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:78:17: error: unexpected token in argument list  
    ; exit code is zero  
                ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:79:13: error: unknown token in expression  
    mov x0, #0  
            ^  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:80:7: error: invalid instruction mnemonic 'exit'  
    ; exit application  
      ^~~~  
/Downloads/boost-1.83.0/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:81:5: error: invalid instruction mnemonic 'bl'  
    bl __exit  
    ^~  
make[2]: *** [libs/context/CMakeFiles/boost_context.dir/src/asm/make_arm64_aapcs_macho_gas.S.o] Error 1  
make[1]: *** [libs/context/CMakeFiles/boost_context.dir/all] Error 2  
make: *** [all] Error 2  
```

---

## Comment 1

> Username: Boris-Rasin  
> Created at: 2023-08-19 23:06:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/42#issuecomment-1685128176  

Boost::context library [boost/libs/context/CMakeLists.txt](https://github.com/boostorg/context/blob/boost-1.83.0/CMakeLists.txt) selects asm sources based on `CMAKE_SYSTEM_PROCESSOR` variable during CMake configuration step.  
  
`CMAKE_SYSTEM_PROCESSOR` specifies single architecture, which in your case happens to be arm64.  
  
So the build fails when trying to build x86_64 part of universal lib.  
  
As a workaround, it should be possible to build arm64 and x86_64 libraries separately, then combine them into universal lib using lipo.  
  
Build arm64 only:  
```  
-DCMAKE_OSX_ARCHITECTURES=arm64  
-DCMAKE_SYSTEM_PROCESSOR=arm64  
```  
  
Build x86_64 only:  
```  
-DCMAKE_OSX_ARCHITECTURES=x86_64  
-DCMAKE_SYSTEM_PROCESSOR=x86_64  
```  
  
The real solution will probably require changes in CMake itself, like support for `$<CMAKE_SYSTEM_PROCESSOR>` generator expression.
