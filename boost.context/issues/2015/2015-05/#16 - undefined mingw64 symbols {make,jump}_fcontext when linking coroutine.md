# #16 - undefined mingw64 symbols {make,jump}_fcontext when linking coroutine [Closed]

> Username: xantares  
> Created at: 2015-05-03 10:16:19 UTC  
> Updated at: 2016-01-23 23:20:00 UTC  
> Closed at: 2016-01-12 08:30:15 UTC  
> Url: https://github.com/boostorg/context/issues/16  

hi,  
  
i'm trying to build boost 1.58 for mingw64 and it fails for at least the i386 target:  
  
```  
toolset=gcc-mingw64 \  
      target-os=windows \  
      variant=release \  
      threading=multi \  
      threadapi=win32 \  
      link=shared,static \  
      runtime-link=shared \  
      --prefix=${pkgdir}/usr/i686-w64-mingw32 \  
      --user-config=user-config.jam \  
      --without-mpi \  
      --without-graph_parallel \  
      --without-python \  
      cxxflags="-std=c++11 -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions --param=ssp-buffer-size=4" \  
      address-model=i686-w64-mingw32 \  
      architecture=x86 \  
      binary-format=pe \  
```  
  
and using this patch:  
http://pkgs.fedoraproject.org/cgit/mingw-boost.git/tree/boost-mingw.patch  
  
i've got the following error:  
  
```  
gcc.link.dll bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_coroutine-mt.dll.a  
bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/detail/coroutine_context.o:coroutine_context.cpp:(.text+0x4a): undefined reference to `_imp__make_fcontext'  
bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/detail/coroutine_context.o:coroutine_context.cpp:(.text+0xc7): undefined reference to `_imp__jump_fcontext'  
```  
  
however it's very similar to what was fixed here:  
https://github.com/boostorg/context/commit/70fab42aa4f5d2138d8c47a231d929c3768d61c9

---

## Comment 1

> Username: xantares  
> Created at: 2015-09-01 13:30:00 UTC  
> Updated at: 2015-09-01 13:33:37 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-136722702  

update: context 1.59 provides now gas assemblies for gcc targetting windows, thanks @olk and that fedora patch won't be needed anymore.  
  
when cross-compiling from linux I had to call b2 with abi=ms to enable these as abi defaults to sysv:  
  
```  
alias asm_context_sources  
   : asm/make_i386_ms_pe_gas.asm  
     asm/jump_i386_ms_pe_gas.asm  
     dummy.cpp  
   : <abi>ms  
     <address-model>32  
     <architecture>x86  
     <binary-format>pe  
     <toolset>gcc  
   ;  
```  
  
Now, I still get the same undefined error in the coroutine library, though make_fcontext is declared as .globl, but I'm not fluent in asm:  
  
```  
.file   "make_i386_ms_pe_gas.asm"  
.text  
.p2align 4,,15  
.globl  _make_fcontext  
```  
  
https://github.com/boostorg/context/blob/boost-1.59.0/src/asm/make_i386_ms_pe_gas.asm

---

## Comment 2

> Username: olk  
> Created at: 2015-09-01 13:37:32 UTC  
> Updated at: 2015-09-01 13:38:03 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-136724467  

cross-compiling assembler is not supported, e.g. GNU as on LINUX != GNU as on Windows  
I assume that you try to compile make_i386_ms_pe_gas.asm (which has opcode for the Windows platform) with the Linux version of GNU as.

---

## Comment 3

> Username: xantares  
> Created at: 2015-09-01 13:51:51 UTC  
> Updated at: 2015-09-01 13:54:13 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-136729353  

@olk I assumed boost used the compiler to read those (mingw-gcc for target i386/x86_64 that is able to produce valid windows object files).  
  
Is there a way to specify the assembler in the jam config as for rc and archiver ?  
  
Else, what is the default rule used ? Should I write a custom rule to use i686|x86_64-w64-mingw32-as similar to those:  
  
```  
actions gas64  
{  
    cpp -x assembler-with-cpp "$(>)" | as --64 -o "$(<)"  
}  
...  
[ make asm/make_arm64_aapcs_elf_gas.o : asm/make_arm64_aapcs_elf_gas.S : @gas64 ]  
```

---

## Comment 4

> Username: olk  
> Created at: 2015-09-01 14:05:12 UTC  
> Updated at: 2015-09-01 14:05:22 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-136732929  

the compiler calls its assembler in order to generate object files from *.asm  
GCC -> GNU as  
MSVC -> MASM  
the regression tests using mingw pass the unit-tests (compiled+executed on Windows, no cross-compiling)  
so my assumtion is that the assembler in your cross-compiling tool chain is unable to understand/compile the op-codes in make_i386_ms_pe_gas.asm

---

## Comment 5

> Username: xantares  
> Created at: 2015-09-01 14:07:12 UTC  
> Updated at: 2015-09-01 14:09:03 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-136733579  

I think my assembler is fine:  
  
```  
$ i686-w64-mingw32-cpp -x assembler-with-cpp src/boost_1_59_0/libs/context/src/asm/make_i386_ms_pe_gas.asm|i686-w64-mingw32-as -o make_i386_ms_pe_gas.o  
$ i686-w64-mingw32-nm make_i386_ms_pe_gas.o  
00000000 b .bss  
00000000 d .data  
         U __exit  
0000005e t finish  
00000049 t found  
00000000 T _make_fcontext  
00000000 t .text  
0000003f t walk  
```  
  
if it were using the host as it would have failed:  
  
```  
$ i686-w64-mingw32-cpp -x assembler-with-cpp src/boost_1_59_0/libs/context/src/asm/make_i386_ms_pe_gas.asm|as -o make_i386_ms_pe_gas.o  
make_i386_ms_pe_gas.asm: Assembler messages:  
make_i386_ms_pe_gas.asm:30: Error: unknown pseudo-op: `.def'  
make_i386_ms_pe_gas.asm:30: Error: unknown pseudo-op: `.scl'  
make_i386_ms_pe_gas.asm:30: Error: Missing symbol name in directive  
make_i386_ms_pe_gas.asm:30: Error: unrecognized symbol type "32"  
make_i386_ms_pe_gas.asm:30: Error: unknown pseudo-op: `.endef'  
make_i386_ms_pe_gas.asm:124: Error: unknown pseudo-op: `.def'  
make_i386_ms_pe_gas.asm:124: Error: unknown pseudo-op: `.scl'  
make_i386_ms_pe_gas.asm:124: Error: Missing symbol name in directive  
make_i386_ms_pe_gas.asm:124: Error: unrecognized symbol type "32"  
make_i386_ms_pe_gas.asm:124: Error: unknown pseudo-op: `.endef'  
```

---

## Comment 6

> Username: xantares  
> Created at: 2015-09-01 14:29:46 UTC  
> Updated at: 2015-09-01 15:17:04 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-136740547  

here is the assembler compilation line, is it ok ?  
  
```  
gcc.compile.asm bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/asm/make_i386_ms_pe_gas.o  
  
    "i686-w64-mingw32-g++" -x assembler-with-cpp -O3 -finline-functions -Wno-inline -Wall -march=i686 -m32 -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_SOURCE -DBOOST_DISABLE_ASSERTS -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -I"." -c -o "bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/asm/make_i386_ms_pe_gas.o" "libs/context/src/asm/make_i386_ms_pe_gas.asm"  
```  
  
then i can see it's linked:  
  
```  
gcc.link.dll bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_context-mt.dll.a  
  
    "i686-w64-mingw32-g++"   "-Wl,--out-implib,bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_context-mt.dll.a" -o "bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_context-mt.dll" -Wl,-h -Wl,libboost_context-mt.dll -shared -Wl,--start-group "bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/asm/make_i386_ms_pe_gas.o" "bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/asm/jump_i386_ms_pe_gas.o" "bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/dummy.o" "bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/windows/stack_traits.o" "bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/execution_context.o" "bin.v2/libs/thread/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_thread_win32-mt.dll.a" "bin.v2/libs/chrono/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_chrono-mt.dll.a" "bin.v2/libs/system/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_system-mt.dll.a"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -march=i686 -mthreads -m32  
```  
  
before the link fcontext symbols are there:  
  
```  
$ i686-w64-mingw32-nm src/build-i686-w64-mingw32/bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/asm/make_i386_ms_pe_gas.o|grep fcontext  
00000000 T _make_fcontext  
```  
  
after link they are not present in the import lib:  
  
```  
$ i686-w64-mingw32-nm src/build-i686-w64-mingw32/bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_context-mt.dll.a|grep fcontext  
```  
  
but here only in the export lib:  
  
```  
$ i686-w64-mingw32-nm bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_context-mt.dll|grep fcontext  
6bac1520 T _jump_fcontext  
6bac14b0 T _make_fcontext  
```  
  
I guess it should be in the import lib (libboost_context-mt.dll.a) as it's the one coroutine is linked against:  
  
```  
gcc.link.dll bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_coroutine-mt.dll.a  
  
    "i686-w64-mingw32-g++"   "-Wl,--out-implib,bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_coroutine-mt.dll.a" -o "bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_coroutine-mt.dll" -Wl,-h -Wl,libboost_coroutine-mt.dll -shared -Wl,--start-group "bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/detail/coroutine_context.o" "bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/exceptions.o" "bin.v2/libs/coroutine/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/windows/stack_traits.o" "bin.v2/libs/context/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_context-mt.dll.a" "bin.v2/libs/thread/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_thread_win32-mt.dll.a" "bin.v2/libs/chrono/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_chrono-mt.dll.a" "bin.v2/libs/system/build/gcc-mingw-mingw64/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_system-mt.dll.a"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -march=i686 -mthreads -m32  
```  
  
So it's a symbol visibility problem ?

---

## Comment 7

> Username: xantares  
> Created at: 2015-09-23 17:04:25 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-142664450  

@olk fedora has a patch for this:  
http://pkgs.fedoraproject.org/cgit/mingw-boost.git/tree/boost-mingw.patch  
  
it adds the following code for jump|make and i386|x86_64:  
  
```  
.section .drectve  
.ascii " -export:\"jump_fcontext\""  
```  
  
could this be applied ?

---

## Comment 8

> Username: olk  
> Created at: 2015-09-27 18:12:56 UTC  
> Updated at: 2015-09-27 18:13:58 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-143581917  

Please submit an verified patch that fixes this issues - I've not the time at the moment to prepare the patch.

---

## Comment 9

> Username: xantares  
> Created at: 2015-09-28 08:06:47 UTC  
> Updated at: 2015-09-28 08:22:38 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-143666190  

@olk, see #22

---

## Comment 10

> Username: olk  
> Created at: 2015-09-28 08:14:17 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-143669843  

the patch from Fedora can not applied because is disables the ABI/BinarayFormat detection

---

## Comment 11

> Username: xantares  
> Created at: 2015-09-28 08:16:14 UTC  
> Updated at: 2015-09-28 08:22:41 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-143670527  

@olk, please read my comments and my patch at #22

---

## Comment 12

> Username: sav-ix  
> Created at: 2016-01-12 08:19:12 UTC  
> Updated at: 2016-01-12 08:20:23 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170834732  

Hi,  
  
Any chances, this error would be fixed in future releases? While building all BOOST components using MinGW-W64 it causes result:  
  
---  
  
...failed updating 83 targets...  
...skipped 22 targets...  
...updated 13542 targets...  
  
---  
  
Though there is no such troubles while building BOOST using Microsoft or Intel Compilers on Windows.  
  
Regards,  
  
Alexander

---

## Comment 13

> Username: olk  
> Created at: 2016-01-12 08:30:15 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170836594  

The code should work- see regression test http://www.boost.org/development/tests/develop/developer/context.html, Windows, host mingw64 -> test pass the unit-tests.

---

## Comment 14

> Username: xantares  
> Created at: 2016-01-12 10:18:15 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170864303  

The fedora patch is still needed to compile from linux:  
https://pkgs.fedoraproject.org/cgit/rpms/mingw-boost.git/tree/boost-mingw.patch  
Please reopen.

---

## Comment 15

> Username: olk  
> Created at: 2016-01-12 10:22:22 UTC  
> Updated at: 2016-01-12 10:22:40 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170865947  

As I already wrote to you - the fedora patch can not be applied because it is invalid (it disables ABI/BinaryFormat detection). The boost regression-test  show that mingw64 is working.

---

## Comment 16

> Username: xantares  
> Created at: 2016-01-12 10:30:04 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170867813  

In this patch I'm only refering to the changes in the asm files, the changes in jamfile are specific to linux. Is there some sort of cross-compilation support in jam that would allow to write this part cleanly ?  
  
I guess regression tests are made on native windows and dont use gnu as ? For linux/gnu as the problem is still here.

---

## Comment 17

> Username: olk  
> Created at: 2016-01-12 10:44:59 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170871682  

cross-compilation is not supported - because boost.build does not detect the ABI/BinaryFormat correctly.

---

## Comment 18

> Username: sav-ix  
> Created at: 2016-01-12 13:35:16 UTC  
> Updated at: 2016-01-12 13:37:29 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170913197  

Unfortunately I can't confirm that "code work".  
Just tried to build recent BOOST-1.60.0 using recent MinGW-W64 (5.3.0-x86_64) with command:  
  
```  
.\b2 install --prefix="%CD%\..\GCC64d" --build-type=complete --without-mpi toolset=gcc-5.3.0 address-model=64 variant=debug link=shared,static threading=multi cxxflags="-std=c++11" -sICU_PATH="%CD%\..\..\libICU-56.1\GCC64d" -sICU_LINK="-L"%CD%\..\..\libICU-56.1\GCC64d\lib" -licuuc -licuin -licudt" -j 2  
```  
  
and got similar errors as before (undefined reference to `_imp__make_fcontext', etc.).  
  
As I understood, Edward Diener made his build using _static_ linking only (http://www.boost.org/development/tests/develop/mingw64.html). So I tried to build BOOST with shell command above for shared and static linking separately, and:  
- for shared build "undefined reference to..." errors reproduced,  
- for static build "undefined reference to..." errors gone.  
  
Thus could you reopen this ticket, because it _really_ looks that error wasn't fixed yet?  
  
In addition, static builds ended with result:  
  
```  
...failed updating 54 targets...  
...skipped 12 targets...  
...updated 13030 targets...  
```  
  
with lot of error messages:  
  
```  
C:/ProgramData/MinGW64/x86_64-w64-mingw32/include/c++/cmath:1119:11: error: '::hypot' has not been declared  
   using ::hypot;  
           ^  
```  
  
Should this information be reported as BOOST build error using MinGW-W64 too?  
  
Regards,  
  
Alexander

---

## Comment 19

> Username: olk  
> Created at: 2016-01-12 13:45:02 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170915386  

C:/ProgramData/MinGW64/x86_64-w64-mingw32/include/c++/cmath:1119:11: error: '::hypot' has not been declared  
   using ::hypot;  
  
has nothing to do with boost.context

---

## Comment 20

> Username: olk  
> Created at: 2016-01-12 13:49:29 UTC  
> Updated at: 2016-01-12 13:50:06 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170916993  

followign data are  required:  
- target platform  
- platform on which the binaries are compiled  
- compiler (version, cmd-line ...)  
- output of b2 ... -d2 (shows properties used by boost.build)  
- which files are selected for compilation (please only files from boost.context)  
- complete error output from the compiler (please only errors related to boost.context; cmath.h:1119 as you mentioned above has to do with your compiler env/setup -> it is not a file from boost.context)

---

## Comment 21

> Username: sav-ix  
> Created at: 2016-01-12 14:15:28 UTC  
> Updated at: 2016-01-12 14:18:01 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170923818  

i agree that "error: '::hypot' ..... has nothing to do with boost.context". Just asked opinion of BOOST community.  
  
Concerning required data by the points:  
1,2. Build and target platform Windows 10,  
3. Used recent version of official MinGW-W64 distribution (http://sourceforge.net/projects/mingw-w64/); g++ -v output:  
Target: x86_64-w64-mingw32  
Thread model: posix  
gcc version 5.3.0 (x86_64-posix-seh-rev0, Built by MinGW-W64 project)  
4. Could you please clarify, should I made a BOOST build using '-d2' option?  
5. I made build of all BOOST components except MPI using a shell command I posted above:  
  
```  
.\b2 install --prefix="%CD%\..\GCC64d" --build-type=complete --without-mpi toolset=gcc-5.3.0 address-model=64 variant=debug link=shared,static threading=multi cxxflags="-std=c++11" -sICU_PATH="%CD%\..\..\libICU-56.1\GCC64d" -sICU_LINK="-L"%CD%\..\..\libICU-56.1\GCC64d\lib" -licuuc -licuin -licudt" -j 2  
```  
  
Should I make a build of boost.context component only?  
6. Log files of build using shell command above added in attachment.   
[boost_mingw-w64_build_logs.zip](https://github.com/boostorg/context/files/87396/boost_mingw-w64_build_logs.zip)  
  
Seems I didn't answered all of your questions. Let me know which information is still missing and which builds logs (with which params) you're interested to receive.  
  
Regards,  
  
Alexander

---

## Comment 22

> Username: olk  
> Created at: 2016-01-12 14:21:54 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-170925464  

please go to context/test directory and excute:  
b2 tollset=gcc-5.3.0 address-model=64 cxxflags="-std=c++11" -sICU_PATH="%CD%....\libICU-56.1\GCC64d" -sICU_LINK="-L"%CD%....\libICU-56.1\GCC64d\lib" -licuuc -licuin -licudt" -d 2

---

## Comment 23

> Username: eldiener  
> Created at: 2016-01-12 19:46:50 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-171031749  

In the mingw64 regression test using gcc-5.3 I have the Microsoft macro assembler for VC++14 in my PATH to handle .asm files if necessary. I seem to recall that boost/context uses Microsoft masm on Windows, but I am not an expert with boost/context and could be wrong.

---

## Comment 24

> Username: sav-ix  
> Created at: 2016-01-12 22:06:20 UTC  
> Updated at: 2016-01-22 06:41:34 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-171075245  

Thank you, Edward.  
  
I'm not familiar with the rules for publishing BOOST regression tests. But there could be inconsistency of information on 'Boost regression: context/develop' page (http://www.boost.org/development/tests/develop/developer/context.html) and the real state of affairs:  
- it specifies that for testing purposes was used 'mingw64' only, while (if I understood correctly your post above) was used binding 'mingw64+MSVC14_masm',  
- for some reason tests were made for static builds only, while for other compilers/platforms tests were made for shared builds too.  
  
If it doesn't violate rules, what about replacing current 'mingw64' regression test results with new ones, which would be made using 'mingw64' only (without any additional build tools), and which cover both shared and static linking?  
It can show the real state of affairs with building boost.context (and all other BOOST components) using recent MinGW-W64.  
  
Regards,  
  
Alexander

---

## Comment 25

> Username: eldiener  
> Created at: 2016-01-13 03:34:03 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-171150677  

I should have specified that VC++14 masm was in the PATH. So is mingw64/gcc as.exe, even before masm in the PATH. But evidently Boost.context on Windows only can use masm.  
  
The reason for using static builds is that I have had numerous problems with Boost Build on Windows and regression testing when using mingw(-64)/gcc and shared libraries. I did specify in the regression test that static builds using the static RTL are being used.

---

## Comment 26

> Username: sav-ix  
> Created at: 2016-01-14 10:26:32 UTC  
> Updated at: 2016-01-22 06:49:44 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-171598263  

Just found solution for ''::hypot' has not been declared' error, and for build using MinGW-W64 with _all_ BOOST packages and _both_ shared and static linking got result:  
  
```  
...failed updating 2 targets...  
...skipped 2 targets...  
...updated 13643 targets...  
```  
  
Build errors using MinGW-W64 relate to 'boost/coroutine' component with _shared_ linking only:  
  
```  
gcc.link.dll bin.v2\libs\coroutine\build\gcc-mingw-5.3.0\debug\threading-multi\libboost_coroutine-mgw53-mt-d-1_60.dll.a  
bin.v2\libs\coroutine\build\gcc-mingw-5.3.0\debug\threading-multi\detail\coroutine_context.o: In function `boost::coroutines::detail::coroutine_context::coroutine_context(void (*)(long long), boost::coroutines::detail::preallocated const&)':  
c:\libBOOST-1.60.0\src/libs/coroutine/src/detail/coroutine_context.cpp:39: undefined reference to `__imp_make_fcontext'  
bin.v2\libs\coroutine\build\gcc-mingw-5.3.0\debug\threading-multi\detail\coroutine_context.o: In function `boost::coroutines::detail::coroutine_context::jump(boost::coroutines::detail::coroutine_context&, long long, bool)':  
c:\libBOOST-1.60.0\src/libs/coroutine/src/detail/coroutine_context.cpp:71: undefined reference to `__imp_jump_fcontext'  
collect2.exe: error: ld returned 1 exit status  
```  
  
Wherein symbols 'make_fcontext' and 'jump_fcontext' available in Boost.context import and export shared binaries, built using Intel or Microsoft compilers. In binaries, built using MinGW-W64, that symbols available only in 'libboost_context-...-.dll', but not in 'libboost_context-...-.dll.a' (as xantares wrote above).  
  
Also I made more quality regression tests report for 'boost/context' and 'boost/coroutine' components ([logs](https://github.com/boostorg/context/files/90201/boost_context_logs.zip) attached). All that tests finishes with errors for builds using MinGW-W64, while there is no such troubles for builds using Microsoft (and Intel) Compiler.  
  
Taking into account the above, is it possible to reopen current Issue?  
  
Regards,  
  
Alexander

---

## Comment 27

> Username: olk  
> Created at: 2016-01-22 08:06:27 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-173838925  

if you look at libs/context/src/asm/ you see files already provided for gnu as on Windows:  
jump_x86_64_ms_pe_gas.asm/jump_x86_64_ms_pe_gas.asm  
you have to check that those files are used to be compiled - aren't they?

---

## Comment 28

> Username: sav-ix  
> Created at: 2016-01-22 08:15:33 UTC  
> Updated at: 2016-01-22 09:01:29 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-173840145  

'jump_x86_64_ms_pe_gas.asm' and 'make_x86_64_ms_pe_gas.asm' are built successfully with MinGW-W64 assembler (their object files available). Also consider, that static builds of Boost.coroutine using MinGW-W64 were made successfully. Thus it's unlikely to be a troubles with MinGW-W64 assembler while building Boost.context.  
  
But for shared builds using MinGW-W64, symbols 'jump_fcontext' and 'make_fcontext' exists only in 'libboost_context-...-.dll', but not in 'libboost_context-...-.dll.a'. While for shared builds using Microsoft or Intel compilers such symbols exists in both 'boost_context-...-.dll' and 'boost_context-...-.lib' libraries.

---

## Comment 29

> Username: xantares  
> Created at: 2016-01-22 09:29:49 UTC  
> Updated at: 2016-01-22 09:40:56 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-173860233  

@sav-ix, exactly.

---

## Comment 30

> Username: olk  
> Created at: 2016-01-22 09:40:39 UTC  
> Updated at: 2016-01-22 09:41:26 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-173862322  

I've a patch but I can only push it in the evening - but you could test the Fedora part in the meanwhile

---

## Comment 31

> Username: olk  
> Created at: 2016-01-22 19:19:52 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174017117  

commit pushed to branch develop

---

## Comment 32

> Username: sav-ix  
> Created at: 2016-01-22 20:46:47 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174041758  

Just tested shared builds of Boost.context and Boost.coroutine components using MinGW-W64 with recent sources from 'boostorg/context' repo. Seems error with missing '*_fcontext' symbols gone. Thanks!  
  
I'll send more detailed report later. But before running tests, could you clarify:  
- while using -std=gnu++11 compiler key, Boost.context shared builds using MinGW-W64 fail, while static finishes successfully ([logs](https://github.com/boostorg/context/files/101401/gnu.11.zip)),  
- while using -std=c++11 compiler key, there is no such troubles.  
  
Is it bug or feature? Does it inherent to Linux GCC too?

---

## Comment 33

> Username: olk  
> Created at: 2016-01-22 21:16:48 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174051554  

you should use -std=c++11 for boost.context  
you could try to compile boost.coroutine without support of C++11

---

## Comment 34

> Username: sav-ix  
> Created at: 2016-01-22 21:23:22 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174052885  

For some reason if shared Boost.context library is built without C++11 support, its regression tests don't start to built. Thus using C++11 support seems to be required parameter.  
  
I'll prepare test report for different configurations with '-std=c++11' option.

---

## Comment 35

> Username: sav-ix  
> Created at: 2016-01-23 06:39:35 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174154238  

Made more detailed test report ([logs](https://github.com/boostorg/context/files/101728/boost_context_coroutine_using_mingw-w64_logs.zip)):  
  
x64 shared - context test build errors "not declared...", "not a member...",  
x64 shared - coroutine "test_symmetric_coroutine.exe" and "test_asymmetric_coroutine.exe" throw errors during run.  
  
x64 static - context binaries build errors "does not name a type..."; tests build failure,  
x64 static - coroutine tests build failure,  
  
I've updated only context sources; all other were from Boost distributive 1.60.0. But there is no static build errors for context using original Boost-1.60.0 sources:  
  
x64 static - context "test_context.exe" throw errors during run,  
x64 static - coroutine "test_symmetric_coroutine.exe" and "test_asymmetric_coroutine.exe" throw errors during run.  
  
Didn't made builds for 'x86' platform, because there is more than enough errors for 'x64'. Let me know, if you're interested in their results.

---

## Comment 36

> Username: sav-ix  
> Created at: 2016-01-23 07:27:14 UTC  
> Updated at: 2016-01-23 08:59:30 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174156832  

Just tested build commands, similar to used by @eldiener for regression 'mingw64' (http://www.boost.org/development/tests/develop/mingw64.html) or 'Win_gcc-5.2' (http://www.boost.org/development/tests/develop/developer/summary.html):  
  
```  
.\b2 --prefix="%CD%\..\GCC64r" --runner=mingw64 --toolsets=gcc-5.3x64 --tag=develop --bjam-toolset=gcc --pjl-toolset=gcc --with-context --with-coroutine  
```  
  
Boost.context and Boost.coroutine shared tests ran successfully ("test_context.exe" reported memory leaks, but it's not important now).  
  
You'll laugh, but during that builds, 'MinGW/.../g++.exe' was not launched even once. _All_ builds were made using 'MSVC/.../cl.exe'. And if MSVC directory is renamed, _any_ binary could be build using build command above.  
  
Thus my question to @eldiener , are you sure that your build commands test Boost builds using MinGW-W64, not MSVC compiler? ([Process Explorer](https://technet.microsoft.com/en-us/sysinternals/processexplorer.aspx) could be a appropriate tool to verify this)  
  
UPD.  
That binaries contained in 'BOOST/bin.v2/libs/.../build/msvc-14.0/...' directories, while if used build command:  
  
```  
.\b2 install --prefix="%CD%\..\GCC64r" toolset=gcc-5.3.0 address-model=64 variant=release link=static threading=multi --with-context --with-coroutine  
```  
  
they contained in 'BOOST/bin.v2/libs/.../build/gcc-mingw-5.3.0/...' directories.

---

## Comment 37

> Username: olk  
> Created at: 2016-01-23 07:47:13 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174157426  

You need to update boost.coroutine (branch develop) too.

---

## Comment 38

> Username: olk  
> Created at: 2016-01-23 09:09:46 UTC  
> Updated at: 2016-01-23 09:22:58 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174164942  

Building on Win7/64bit + mingw64 (migw-4.8.3?)  
  
boost.context (C++11):  
'b2 --toolset=gcc address-model=64 cxxflags="-Wno-long-long"' unit-tests successful  
  
boost.coroutine (C++03):  
'b2 --toolset=gcc' unit-tests successful  
'b2 --toolset=gcc address-model=64 cxxflags="-Wno-long-long"' unit-tests successful  
  
boost.coroutine2 (C++11):  
'b2 --toolset=gcc address-model=64 cxxflags="-std=c++11 -Wno-long-long"' unit-tests successful  
- verified that g++ is invoked  
- C++11:  boost.build caches the result of the feature check for constexpr, auto_declaration etc. -> maybe cached values of previous runs (on other libs) prevent execution the unit-tests  
- cross compiling NOT tested

---

## Comment 39

> Username: sav-ix  
> Created at: 2016-01-23 14:59:19 UTC  
> Updated at: 2016-01-23 19:37:38 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174192133  

@olk, your build commands missing one important switch - "link=shared". They built static libraries only, which has no errors even before recent patch.  
  
Now I've updated Boost-1.60.0 sources with files from:  
https://github.com/boostorg/context/tree/develop  
https://github.com/boostorg/coroutine/tree/develop  
  
For builds using Win10 64bit + MinGW-W64 x86_64-5.3.0:  
- without "-std=c++11" option:  
  -- x64 shared - context binaries built successfully, tests not built,  
  -- x64 shared - coroutine binaries built successfully, tests run successfully,  
  -- x64 static - context binaries built successfully, tests not built,  
  -- x64 static - coroutine binaries built successfully, tests run successfully,  
- with "-std=c++11" option:  
  -- x64 shared - context binaries built successfully, tests run successfully,  
  -- x64 shared - coroutine binaries built successfully, tests run successfully,  
  -- x64 static - context binaries built successfully, tests run successfully,  
  -- x64 static - coroutine binaries built successfully, tests run successfully,  
  
[Logs](https://github.com/boostorg/context/files/101894/boost_mingw-w64_x64_logs.zip) include used build commands.  
  
Also found a flaw in Boost build system (has nothing to do with boost.context):  
While using commands 'bootstrap mingw' and 'b2 ...  --toolset=gcc ...' only, Boost build system still detects installed MSVC and create build scripts 'b2_msvc_14.0_vcvarsall_*.cmd' in TEMP directory. The only way to prevent this is to rename MSVC directory before building Boost.  
  
UPD.  
@olk -> "verified that g++ is invoked"  
Did you tried command:  
  
```  
.\b2 --runner=mingw64 --toolsets=gcc-5.3x64 --tag=develop --bjam-toolset=gcc --pjl-toolset=gcc --with-context --with-coroutine  
```  
  
which is similar to used for official Boost regression tests using MinGW-W64 (http://www.boost.org/development/tests/develop/mingw64.html). On my system it invokes 'MSVC/.../cl.exe' but not 'MinGW-W64/.../g++.exe'.

---

## Comment 40

> Username: olk  
> Created at: 2016-01-23 15:57:35 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174195954  

so - it seams to be fixed (using 'link=shared'):  
- boost.context -> unit-tests are only executed with '-std=c++11'  
- boost.coroutine -> unit-tests are executed with and without  '-std=c++11'  
  correct?!

---

## Comment 41

> Username: sav-ix  
> Created at: 2016-01-23 16:02:41 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174196248  

Yes (you can see it from logs in my previous post).  
And to receive successful test builds using MinGW-W64 for some configurations (e.g. x64_C++11_shared_context, x64_shared_coroutine, etc.) you have to make about 5 builds or more (at least from my experience).

---

## Comment 42

> Username: olk  
> Created at: 2016-01-23 16:25:13 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174197860  

I don't know why you have to call it multiple times - sounds odd. I'm not much familiar with boost..build.

---

## Comment 43

> Username: sav-ix  
> Created at: 2016-01-23 16:40:17 UTC  
> Updated at: 2016-01-23 16:41:15 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174198833  

My Boost.build questions are not to you. Thanks again, that fixed '*_fcontext' errors. Now _all_ BOOST components could be build using MinGW with shared+static configurations.  
  
More interesting, which compiler was _really_ used for [Win_gcc-5.2](http://www.boost.org/development/tests/develop/Win_gcc-5-2.html) and [mingw64](http://www.boost.org/development/tests/develop/mingw64.html) regression tests. Hope @eldiener will shed light on this question.

---

## Comment 44

> Username: eldiener  
> Created at: 2016-01-23 19:52:28 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174215905  

I used mingw-64/gcc-5.2 for testing with vc++14 macro assembler in the PATH.

---

## Comment 45

> Username: sav-ix  
> Created at: 2016-01-23 19:59:44 UTC  
> Updated at: 2016-01-23 20:03:03 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174216297  

I see. In my case was unable to built Boost using MinGW-W64 with your build command, when MSVC directory was renamed. And when MSVC directory was not renamed - used only Microsoft compiler.  
  
As @olk fixed all errors, what about move to pure MinGW builds (without using 3rd party stuff)? Just tried to build Boost for <debug,release><shared,static> configurations using only MinGW-W64 and _all_ components were built successfully.

---

## Comment 46

> Username: eldiener  
> Created at: 2016-01-23 23:20:00 UTC  
> Url: https://github.com/boostorg/context/issues/16#issuecomment-174231527  

The only 3rd part stuff was putting VC++ 14 macro assembler at the end of my PATH. If it is now not needed it will not be called. So I do not understand how putting the Microsoft macro assembler at the end of my PATH could affect anything if this problem has been addressed and it is no longer needed.
