# #308 - many `unrecognized opcode` errors under ppc64le cross-compilation [Closed]

> Username: ReenigneArcher  
> Created at: 2025-08-02 13:20:44 UTC  
> Updated at: 2025-08-02 17:37:09 UTC  
> Closed at: 2025-08-02 17:37:09 UTC  
> Url: https://github.com/boostorg/container/issues/308  

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

---

## Comment 1

> Username: ReenigneArcher  
> Created at: 2025-08-02 17:37:09 UTC  
> Url: https://github.com/boostorg/container/issues/308#issuecomment-3146635036  

Sorry, opened in the wrong repo.
