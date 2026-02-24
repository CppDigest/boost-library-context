# #190 - arm64 Apple M1 Silicon not supported [Closed]

> Username: brandonros  
> Created at: 2021-11-03 05:07:39 UTC  
> Updated at: 2024-01-19 04:23:15 UTC  
> Closed at: 2021-11-05 03:42:07 UTC  
> Url: https://github.com/boostorg/context/issues/190  

```  
 cmake -S . -B "output" && cmake --build output  
-- Boost: static libraries, MPI OFF, Python OFF, testing OFF  
-- Boost.Context: architecture arm64, binary format mach-o, ABI sysv, assembler gas, suffix .S, implementation fcontext  
-- Boost.Fiber: NUMA target OS is none  
-- Boost.Iostreams: ZLIB ON, BZip2 ON, LZMA ON, Zstd ON  
-- Boost.Locale: iconv ON, ICU OFF, POSIX ON, std ON, winapi OFF  
-- Boost.Math: standalone mode OFF  
-- Boost.Stacktrace: noop ON, backtrace OFF, addr2line ON, basic ON, windbg OFF, windbg_cached OFF  
-- Boost.Thread: threading API is pthread  
Detected version: 1.0.3  
-- Dynamically linking with Boost  
-- Could NOT find Doxygen (missing: DOXYGEN_EXECUTABLE)   
-- Configuring done  
CMake Error at deps/boost/libs/context/CMakeLists.txt:181 (add_library):  
  Cannot find source file:  
  
    src/asm/make_arm64_sysv_macho_gas.S  
  
  Tried extensions .c .C .c++ .cc .cpp .cxx .cu .mpp .m .M .mm .h .hh .h++  
  .hm .hpp .hxx .in .txx .f .F .for .f77 .f90 .f95 .f03 .ispc  
  
  
CMake Error at deps/boost/libs/context/CMakeLists.txt:181 (add_library):  
  No SOURCES given to target: boost_context  
  
  
CMake Generate step failed.  Build files cannot be regenerated correctly.  
```

---

## Comment 1

> Username: brandonros  
> Created at: 2021-11-03 05:09:06 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-958659901  

```  
$ uname -a  
Darwin Brandons-MacBook-Air.local 20.6.0 Darwin Kernel Version 20.6.0: Mon Aug 30 06:12:20 PDT 2021; root:xnu-7195.141.6~3/RELEASE_ARM64_T8101 arm64  
```

---

## Comment 2

> Username: olk  
> Created at: 2021-11-03 06:09:55 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-958677304  

boost.build ist the offical build tool.  
CMakeLists.txt generates the wrong file name because it selects the wrong ABI - it must be aapcs instead of sysv.  
  
@pdimov Because you introduced cmake support, could you fix it please

---

## Comment 3

> Username: pdimov  
> Created at: 2021-11-03 13:54:45 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-959137426  

@brandonros You should be able to work around this by setting `BOOST_CONTEXT_ABI` manually to `aapcs`, e.g. by using `-DBOOST_CONTEXT_ABI=aapcs` on the command line.  
  
@olk Ordinarily I wouldn't be able to, as I have no access to any ARM or MIPS machines, which is why the original autodetection logic only worked on Intel. However, I see that a pull request has added autodetection for BOOST_CONTEXT_ARCHITECTURE, so I should be able to copy that logic for BOOST_CONTEXT_ABI as well.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-11-03 14:22:50 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-959226337  

This should work: https://github.com/boostorg/context/commit/b996250074f23d87c599b88b43538176c9a369d9  
  
I have no way to test it though. It copies the logic from build/Jamfile and uses the same approach as the architecture detection.

---

## Comment 5

> Username: pdimov  
> Created at: 2021-11-03 15:43:14 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-959497147  

Note that the deadline for 1.78 beta is today, so if you want this to go there, you need to merge it to master soon (a few hours.)

---

## Comment 6

> Username: olk  
> Created at: 2021-11-03 16:36:50 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-959678696  

> Note that the deadline for 1.78 beta is today, so if you want this to go there, you need to merge it to master soon (a few hours.)  
  
merged to master - ty

---

## Comment 7

> Username: brandonros  
> Created at: 2021-11-04 00:50:07 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-960345831  

```  
[ 12%] Built target boost_chrono  
Consolidate compiler generated dependencies of target boost_container  
[ 14%] Built target boost_container  
Scanning dependencies of target boost_context  
[ 15%] Building ASM object deps/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/make_arm64_aapcs_macho_gas.S.o  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:59:21: error: unexpected token in argument list  
    ; shift address in x0 (allocated stack) to lower 16 byte boundary  
                    ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:60:5: error: unknown use of instruction mnemonic without a size suffix  
    and x0, x0, ~0xF  
    ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:62:21: error: unexpected token in argument list  
    ; reserve space for context-data on context-stack  
                    ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:63:17: error: unknown token in expression  
    sub x0, x0, #0xb0  
                ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:65:17: error: unexpected token in argument list  
    ; third arg of make_fcontext() == address of context-function  
                ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:66:21: error: unexpected token in argument list  
    ; store address as a PC to jump in  
                    ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:67:13: error: brackets expression not supported on this target  
    str x2, [x0, #0xa0]  
            ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:69:5: error: invalid instruction mnemonic 'adr'  
    adr x1, finish  
    ^~~  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:71:20: error: unexpected token in argument list  
    ; save address of finish as return-address for context-function  
                   ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:72:15: error: unexpected token in argument list  
    ; will be entered after context-function returns (LR register)  
              ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:73:13: error: brackets expression not supported on this target  
    str x1, [x0, #0x98]  
            ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:75:5: error: unknown use of instruction mnemonic without a size suffix  
    ret lr ; return pointer to context-data (x0)  
    ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:75:29: error: unexpected token in argument list  
    ret lr ; return pointer to context-data (x0)  
                            ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:78:17: error: unexpected token in argument list  
    ; exit code is zero  
                ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:79:13: error: unknown token in expression  
    mov x0, #0  
            ^  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:80:7: error: invalid instruction mnemonic 'exit'  
    ; exit application  
      ^~~~  
/Users/brandonros/Desktop/redis-j2534-rpc/deps/boost/libs/context/src/asm/make_arm64_aapcs_macho_gas.S:81:5: error: invalid instruction mnemonic 'bl'  
    bl __exit  
    ^~  
make[2]: *** [deps/boost/libs/context/CMakeFiles/boost_context.dir/src/asm/make_arm64_aapcs_macho_gas.S.o] Error 1  
make[1]: *** [deps/boost/libs/context/CMakeFiles/boost_context.dir/all] Error 2  
make: *** [all] Error 2  
```  
  
@pdimov

---

## Comment 8

> Username: pdimov  
> Created at: 2021-11-04 00:52:50 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-960346736  

Does it work when you build Context using b2?

---

## Comment 9

> Username: pdimov  
> Created at: 2021-11-04 01:00:58 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-960349751  

What's the entire output of the cmake configure phase with a clean build directory? Something seems to be wrong with the assembler, but it's not clear what.

---

## Comment 10

> Username: olk  
> Created at: 2021-11-04 08:49:31 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-960561114  

Seams to me that the wrong assembler tool is selected because of comments are causing errors

---

## Comment 11

> Username: pdimov  
> Created at: 2021-11-05 00:08:00 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-961526647  

Another thing to try is building with `cmake --build . -v` or `VERBOSE=1 make` so that the commands are displayed; this will show us which assembler is being used. Also, make sure you use the latest CMake, something might have been fixed in its assembler selection.

---

## Comment 12

> Username: brandonros  
> Created at: 2021-11-05 03:42:07 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-961604872  

I was doing something dumb with `CMAKE_OSX_ARCHITECTURES`. This is fixed, thank you both of you.

---

## Comment 13

> Username: zacharyfmarion  
> Created at: 2024-01-19 04:23:14 UTC  
> Url: https://github.com/boostorg/context/issues/190#issuecomment-1899746830  

@brandonros I'm running into the same error when compiling for android for `arm64-v8a` - do you happen to remember how you fixed this?
