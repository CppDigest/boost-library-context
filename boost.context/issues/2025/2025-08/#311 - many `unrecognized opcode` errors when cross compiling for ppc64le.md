# #311 - many `unrecognized opcode` errors when cross compiling for ppc64le [Open]

> Username: ReenigneArcher  
> Created at: 2025-08-02 17:38:38 UTC  
> Updated at: 2025-08-14 09:28:57 UTC  
> Url: https://github.com/boostorg/context/issues/311  

When cross-compiling for ppc64le from x86_64 Ubuntu I get the following errors.  
  
```  
[  0%] Building CXX object _deps/boost-build/libs/atomic/CMakeFiles/boost_atomic.dir/src/lock_pool.cpp.o  
[  0%] Building CXX object _deps/boost-build/libs/chrono/CMakeFiles/boost_chrono.dir/src/chrono.cpp.o  
[  0%] Building C object _deps/boost-build/libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o  
[  0%] Building ASM object _deps/boost-build/libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_sysv_elf_gas.S.o  
make_x86_64_sysv_elf_gas.S: Assembler messages:  
make_x86_64_sysv_elf_gas.S:59: Error: unrecognized opcode: `movq'  
make_x86_64_sysv_elf_gas.S:62: Error: unrecognized opcode: `andq'  
make_x86_64_sysv_elf_gas.S:66: Error: unrecognized opcode: `leaq'  
make_x86_64_sysv_elf_gas.S:70: Error: unrecognized opcode: `movq'  
make_x86_64_sysv_elf_gas.S:73: Error: unrecognized opcode: `stmxcsr'  
make_x86_64_sysv_elf_gas.S:75: Error: unrecognized opcode: `fnstcw'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:84: Error: unrecognized opcode: `leaq'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:87: Error: unrecognized opcode: `movq'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:90: Error: unrecognized opcode: `leaq'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:93: Error: unrecognized opcode: `movq'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:133: Error: unrecognized opcode: `ret'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:146: Error: unrecognized opcode: `push'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:149: Error: unrecognized opcode: `jmp'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:154: Error: unrecognized opcode: `xorq'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:156: Error: unrecognized opcode: `call'  
/home/runner/work/build-deps/build-deps/build/_deps/boost-src/libs/context/src/asm/make_x86_64_sysv_elf_gas.S:157: Error: unrecognized opcode: `hlt'  
make[2]: *** [_deps/boost-build/libs/context/CMakeFiles/boost_context.dir/build.make:78: _deps/boost-build/libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_sysv_elf_gas.S.o] Error 1  
make[2]: Leaving directory '/home/runner/work/build-deps/build-deps/build'  
make[1]: *** [CMakeFiles/Makefile2:2041: _deps/boost-build/libs/context/CMakeFiles/boost_context.dir/all] Error 2  
make[1]: *** Waiting for unfinished jobs....  
```  
  
https://github.com/LizardByte/build-deps/actions/runs/16693866779/job/47255227124?pr=476#step:15:36  
  
I should add that this is with boost 1.87.0, as we're stuck with this version until the codebase is updated to support code that was removed in 1.88+. If this has already been fixed, I apologize.

---

## Comment 1

> Username: mbs-c  
> Created at: 2025-08-13 16:56:20 UTC  
> Url: https://github.com/boostorg/context/issues/311#issuecomment-3184707380  

I can confirm that all cmake builds for power are affected. This has nothing to do with cross-compilation, as I can reproduce the issue on a Power machine with both Linux and AIX. The `CMAKE_SYSTEM_PROCESSOR` values `powerpc` (AIX) and `ppc64le` (Linux on Power) are simply not handled in `CMakeLists.txt`.

---

## Comment 2

> Username: mbs-c  
> Created at: 2025-08-13 17:29:16 UTC  
> Url: https://github.com/boostorg/context/issues/311#issuecomment-3184823399  

Quick and dirty workaround for Linux on Power:  
```cmake  
if (CMAKE_SYSTEM_PROCESSOR STREQUAL "ppc64le") # Linux on Power  
  set(BOOST_CONTEXT_ARCHITECTURE "ppc64")  
endif ()  
```

---

## Comment 3

> Username: ReenigneArcher  
> Created at: 2025-08-14 02:04:47 UTC  
> Url: https://github.com/boostorg/context/issues/311#issuecomment-3186474303  

Confirming that worked for me as well. Thank you!

---

## Comment 4

> Username: mbs-c  
> Created at: 2025-08-14 09:28:57 UTC  
> Url: https://github.com/boostorg/context/issues/311#issuecomment-3187751998  

I'm still struggling with AIX, though. The following should work (assuming `OBJECT_MODE=64`):  
  
```cmake  
if (CMAKE_SYSTEM_PROCESSOR STREQUAL "powerpc")  
  set(BOOST_CONTEXT_ARCHITECTURE "ppc64")  
  set(BOOST_CONTEXT_BINARY_FORMAT "xcoff")  
endif ()  
```  
  
I'm getting strange "undefined symbol" errors from the assembler, though:  
  
```text  
   "/usr/bin/as" -a64 -many -o CMakeFiles/boost_context.dir/src/asm/make_ppc64_sysv_xcoff_gas.S.o CMakeFiles/boost_context.dir/src/asm/make_ppc64_sysv_xcoff_gas.s  
  Assembler:  
  CMakeFiles/boost_context.dir/src/asm/make_ppc64_sysv_xcoff_gas.s: line 13: undefined symbol ".make_fcontext"  
  CMakeFiles/boost_context.dir/src/asm/make_ppc64_sysv_xcoff_gas.s: line 72: undefined symbol "._exit"  
  .ibm-clang: error: assembler command failed with exit code 1 (use -v to see invocation)  
  
   "/usr/bin/as" -a64 -many -o CMakeFiles/boost_context.dir/src/asm/ontop_ppc64_sysv_xcoff_gas.S.o CMakeFiles/boost_context.dir/src/asm/ontop_ppc64_sysv_xcoff_gas.s  
  Assembler:  
  CMakeFiles/boost_context.dir/src/asm/ontop_ppc64_sysv_xcoff_gas.s: line 13: undefined symbol ".ontop_fcontext"  
  .ibm-clang: error: assembler command failed with exit code 1 (use -v to see invocation)  
  
   "/usr/bin/as" -a64 -many -o CMakeFiles/boost_context.dir/src/asm/jump_ppc64_sysv_xcoff_gas.S.o CMakeFiles/boost_context.dir/src/asm/jump_ppc64_sysv_xcoff_gas.s  
  Assembler:  
  CMakeFiles/boost_context.dir/src/asm/jump_ppc64_sysv_xcoff_gas.s: line 13: undefined symbol ".jump_fcontext"  
  .ibm-clang: error: assembler command failed with exit code 1 (use -v to see invocation)  
```
