# #170 - How to compile boost.context with clang for Apple Silicon on macos? [Closed]

> Username: naeco2008  
> Created at: 2021-05-28 10:32:35 UTC  
> Updated at: 2021-06-27 16:14:25 UTC  
> Closed at: 2021-06-27 07:48:59 UTC  
> Url: https://github.com/boostorg/context/issues/170  

Hi,   
I am trying to build boost 1.68.0 with clang12.5 for Apple Silicon. I used the below command to build:  
  
> `./bjam -j16 toolset=clang --build-dir=bin.x64 cxxflags="-arch x86_64 -arch arm64 -fPIC -g -fvisibility=hidden -fvisibility-inlines-hidden -ftemplate-depth=1024 -std=c++11 -stdlib=libc++ -DBOOST_NO_AUTO_PTR" linkflags="-stdlib=libc++"  variant=debug link=static --layout=versioned address-model=64 architecture=combined  threading=multi --without-mpi  --stagedir=lib/clang/x64/debug/ stage  
  
Before the build, I changed the "jump_combined_sysv_macho_gas.S" "make_combined_sysv_macho_gas.S" and "ontop_combined_sysv_macho_gas.S" in " libs/context/src/asm" like below:  
  
> `#if defined(__i386__)  
    #include "ontop_i386_sysv_macho_gas.S"  
#elif defined(__x86_64__)  
    #include "ontop_x86_64_sysv_macho_gas.S"  
#elif defined(__ppc__)  
    #include "ontop_ppc32_sysv_macho_gas.S"  
#elif defined(__ppc64__)  
    #include "ontop_ppc64_sysv_macho_gas.S"  
#elif defined(__arm__)  
    #include "ontop_arm_aapcs_macho_gas.S"  
#elif defined(__arm64__)  
    #include "ontop_arm64_aapcs_macho_gas.S"  
#else  
    #error "No arch's"  
#endif  
  
Boost can be built successfully without any errors. But when I consume this boost library, I always get the below errors:  
  
>undefined symbols for architecture arm64:  
  "_jump_fcontext", referenced from:  
      boost::coroutines::detail::coroutine_context::jump(boost::coroutines::detail::coroutine_context&, void*) in libboost_coroutine-clang-darwin120-mt-d-c64-1_68.a(coroutine_context.o)  
  "_make_fcontext", referenced from:  
      boost::coroutines::detail::coroutine_context::coroutine_context(void (*)(boost::context::detail::transfer_t), boost::coroutines::detail::preallocated const&) in libboost_coroutine-clang-darwin120-mt-d-c64-1_68.a(coroutine_context.o)  
ld: symbol(s) not found for architecture arm64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
Do you know how to solve this issue? This blocks me for several days.   
  
Thanks,   
-Naeco2008

---

## Comment 1

> Username: giordano  
> Created at: 2021-05-31 22:40:16 UTC  
> Updated at: 2021-05-31 23:15:54 UTC  
> Url: https://github.com/boostorg/context/issues/170#issuecomment-851706212  

I have the same issue with Boost 1.76.0 and vanilla LLVM v11:  
```  
...failed darwin.link.dll bin.v2/libs/fiber/build/darwin-6.0/release/target-os-darwin/threading-multi/visibility-hidden/libboost_fiber.dylib...  
darwin.link.dll bin.v2/libs/coroutine/build/darwin-6.0/release/target-os-darwin/threadapi-pthread/threading-multi/visibility-hidden/libboost_coroutine.dylib  
Undefined symbols for architecture arm64:  
  "_jump_fcontext", referenced from:  
      __ZN5boost10coroutines6detail17coroutine_context4jumpERS2_Pv in coroutine_context.o  
  "_make_fcontext", referenced from:  
      __ZN5boost10coroutines6detail17coroutine_contextC2EPFvNS_7context6detail10transfer_tEERKNS1_12preallocatedE in coroutine_context.o  
      __ZN5boost10coroutines6detail17coroutine_contextC1EPFvNS_7context6detail10transfer_tEERKNS1_12preallocatedE in coroutine_context.o  
ld: symbol(s) not found for architecture arm64  
clang-11: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "c++" -dynamiclib -Wl,-single_module -install_name "libboost_coroutine.dylib"  -o "bin.v2/libs/coroutine/build/darwin-6.0/release/target-os-darwin/threadapi-pthread/threading-multi/visibility-hidden/libboost_coroutine.dylib" "bin.v2/libs/coroutine/build/darwin-6.0/release/target-os-darwin/threadapi-pthread/threading-multi/visibility-hidden/detail/coroutine_context.o" "bin.v2/libs/coroutine/build/darwin-6.0/release/target-os-darwin/threadapi-pthread/threading-multi/visibility-hidden/exceptions.o" "bin.v2/libs/coroutine/build/darwin-6.0/release/target-os-darwin/threadapi-pthread/threading-multi/visibility-hidden/posix/stack_traits.o" "bin.v2/libs/chrono/build/darwin-6.0/release/target-os-darwin/threading-multi/visibility-hidden/libboost_chrono.dylib" "bin.v2/libs/context/build/darwin-6.0/release/target-os-darwin/threading-multi/visibility-hidden/libboost_context.dylib" "bin.v2/libs/thread/build/darwin-6.0/release/target-os-darwin/threadapi-pthread/threading-multi/visibility-hidden/libboost_thread.dylib"      -headerpad_max_install_names -fPIC -fvisibility=hidden -fvisibility-inlines-hidden -Wl,-dead_strip -no_dead_strip_inits_and_terms -stdlib=libc++ -arch arm64   
```  
The library `libboost_context.dylib` doesn't have the `_jump_fcontext` symbol:  
```console  
sandbox:${WORKSPACE}/srcdir/boost_1_76_0 # nm bin.v2/libs/context/build/darwin-6.0/release/target-os-darwin/threading-multi/visibility-hidden/libboost_context.dylib  
0000000000007ed8 s GCC_except_table0  
0000000000007ee8 s GCC_except_table1  
0000000000007ef8 s GCC_except_table4  
0000000000007f08 s GCC_except_table5  
0000000000007f18 s GCC_except_table8  
000000000000c030 b __MergedGlobals  
0000000000007dd0 t __ZN12_GLOBAL__N_116stacksize_limit_EP6rlimit  
0000000000007e0c t __ZN12_GLOBAL__N_19pagesize_EPm  
0000000000007d50 T __ZN5boost7context12stack_traits12default_sizeEv  
0000000000007c64 T __ZN5boost7context12stack_traits12is_unboundedEv  
0000000000007d60 T __ZN5boost7context12stack_traits12maximum_sizeEv  
0000000000007d58 T __ZN5boost7context12stack_traits12minimum_sizeEv  
0000000000007ce0 T __ZN5boost7context12stack_traits9page_sizeEv  
                 U __ZNSt3__111__call_onceERVmPvPFvS2_E  
0000000000007dfc t __ZNSt3__117__call_once_proxyINS_5tupleIJRFvP6rlimitEOS3_EEEEEvPv  
0000000000007e38 t __ZNSt3__117__call_once_proxyINS_5tupleIJRFvPmEOS2_EEEEEvPv  
                 U __ZSt9terminatev  
0000000000007df0 t ___clang_call_terminate  
                 U ___cxa_begin_catch  
                 U ___gxx_personality_v0  
000000000000c028 d __dyld_private  
                 U _getrlimit  
                 U _sysconf  
                 U dyld_stub_binder  
```  
  
These are the arguments I'm passing to `b2`:  
```console  
sandbox:${WORKSPACE}/srcdir/boost_1_76_0 # ./b2 toolset=darwin-6.0 target-os=darwin abi=sysv architecture=arm binary-format=mach-o link=shared variant=release --prefix=/workspace/destdir --without-python --layout=system install  
sh: /usr/bin/sw_vers: not found  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : arm (cached) [1]  
error: No best alternative for libs/context/build/asm_sources  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>qcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>n64 <address-model>64 <architecture>mips1 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>n64 <address-model>64 <architecture>mips1 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>power <binary-format>mach-o <threading>multi  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>riscv <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>s390x <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>embarcadero  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>borland  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>embarcadero  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>x86 <binary-format>mach-o <threading>multi  
        not matched  
    next alternative: required properties: <abi>sysv <architecture>combined <binary-format>mach-o <threading>multi  
        not matched  
```

---

## Comment 2

> Username: olk  
> Created at: 2021-06-01 15:48:53 UTC  
> Url: https://github.com/boostorg/context/issues/170#issuecomment-852234485  

I'm sorry I don't use Apple stuff nor do I have access to it...  
Some compilers/assembler name the function as _jump_fcontext others jump_fcontext...so you have to figure out what your platform expects. Additionally you need to make proper entries in build/Jamfile for your specific platform/compiler configuration (MacOS/clang) so boost.build knows which (assembler) files to select for compiling the lib.

---

## Comment 3

> Username: giordano  
> Created at: 2021-06-01 15:53:05 UTC  
> Updated at: 2021-06-01 15:54:57 UTC  
> Url: https://github.com/boostorg/context/issues/170#issuecomment-852237525  

Yeah, I think for this platform we'd need `abi=sysv architecture=arm binary-format=mach-o`, but there is no correspondent assembly for that in [`build/Jamfile.v2`](https://github.com/boostorg/context/blob/7c451db14ad71749423e07a2f25d673dc8d19b1c/build/Jamfile.v2).  Also, this isn't a problem with underscores, the `libboost-context.dylib` library doesn't have any "jump" symbol at all:  
```console  
sandbox:${WORKSPACE}/srcdir/boost_1_76_0 # nm bin.v2/libs/context/build/darwin-6.0/release/target-os-darwin/threading-multi/visibility-hidden/libboost_context.dylib  
0000000000007ed8 s GCC_except_table0  
0000000000007ee8 s GCC_except_table1  
0000000000007ef8 s GCC_except_table4  
0000000000007f08 s GCC_except_table5  
0000000000007f18 s GCC_except_table8  
000000000000c030 b __MergedGlobals  
0000000000007dd0 t __ZN12_GLOBAL__N_116stacksize_limit_EP6rlimit  
0000000000007e0c t __ZN12_GLOBAL__N_19pagesize_EPm  
0000000000007d50 T __ZN5boost7context12stack_traits12default_sizeEv  
0000000000007c64 T __ZN5boost7context12stack_traits12is_unboundedEv  
0000000000007d60 T __ZN5boost7context12stack_traits12maximum_sizeEv  
0000000000007d58 T __ZN5boost7context12stack_traits12minimum_sizeEv  
0000000000007ce0 T __ZN5boost7context12stack_traits9page_sizeEv  
                 U __ZNSt3__111__call_onceERVmPvPFvS2_E  
0000000000007dfc t __ZNSt3__117__call_once_proxyINS_5tupleIJRFvP6rlimitEOS3_EEEEEvPv  
0000000000007e38 t __ZNSt3__117__call_once_proxyINS_5tupleIJRFvPmEOS2_EEEEEvPv  
                 U __ZSt9terminatev  
0000000000007df0 t ___clang_call_terminate  
                 U ___cxa_begin_catch  
                 U ___gxx_personality_v0  
000000000000c028 d __dyld_private  
                 U _getrlimit  
                 U _sysconf  
                 U dyld_stub_binder  
```

---

## Comment 4

> Username: giordano  
> Created at: 2021-06-01 15:59:24 UTC  
> Url: https://github.com/boostorg/context/issues/170#issuecomment-852242247  

To be clear, the error I reported above happens when building Boost 1.76.0, so it isn't me who has to figure out the name of the function: the build system fails to build the `libboost_coroutine` library because something references the `*_fcontext` functions, which however aren't defined anywhere.

---

## Comment 5

> Username: imciner2  
> Created at: 2021-06-18 01:44:21 UTC  
> Url: https://github.com/boostorg/context/issues/170#issuecomment-863669877  

Are you sure that is the architecture triple? I thought it would be `abi=aapcs architecture=arm64 binary-format=mach-o` on the M1 (at least that would be my reading of https://developer.apple.com/documentation/xcode/writing-arm64-code-for-apple-platforms). This does have some assembly already in there, so it may just be a matter of detecting the proper architecture in the build script then.

---

## Comment 6

> Username: giordano  
> Created at: 2021-06-19 13:42:52 UTC  
> Url: https://github.com/boostorg/context/issues/170#issuecomment-864408157  

I confirm I was able to successfully build boost.context by configuring it with `abi=aapcs architecture=arm64 binary-format=mach-o`, and I was also able to `dlopen` on an M1 MacBook the built library.  Thanks!

---

## Comment 7

> Username: olk  
> Created at: 2021-06-27 07:48:59 UTC  
> Updated at: 2021-06-27 07:51:24 UTC  
> Url: https://github.com/boostorg/context/issues/170#issuecomment-869118037  

Allowed values for property <architecture> are:  
``x86`, `ia64`, `sparc`, `power`, `mips1`, `mips2`, `mips3`, `mips4`, `mips32`, `mips32r2`, `mips64`, `parisc`, `arm`, `s390x`, `combined`, `combined-x86-power``  
  
Therefore: `abi=aapcs architecture=arm64 binary-format=mach-o` is wrong, it should be `abi=aapcs architecture=arm address-model=64 binary-format=mach-o`.  
  
I'm wondering the difficulties to build boost.context because context/build/Jamfile.v2 contains:  
```  
alias asm_sources  
   : asm/make_arm64_aapcs_macho_gas.S  
     asm/jump_arm64_aapcs_macho_gas.S  
     asm/ontop_arm64_aapcs_macho_gas.S  
   : <abi>aapcs  
     <address-model>64  
     <architecture>arm  
     <binary-format>mach-o  
     <toolset>clang  
   ;  
```

---

## Comment 8

> Username: giordano  
> Created at: 2021-06-27 13:57:23 UTC  
> Url: https://github.com/boostorg/context/issues/170#issuecomment-869168014  

Right, double checking the [options I actually used](https://github.com/JuliaPackaging/Yggdrasil/blob/362a6c7ded35fd56bc4655809d03fd51eb8616d8/B/boost/build_tarballs.jl#L39-L47) they are `abi=aapcs binary-format=mach-o` and the build log shows  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : arm [1]  
```

---

## Comment 9

> Username: olk  
> Created at: 2021-06-27 16:13:35 UTC  
> Updated at: 2021-06-27 16:14:25 UTC  
> Url: https://github.com/boostorg/context/issues/170#issuecomment-869188161  

> Right, double checking the [options I actually used](https://github.com/JuliaPackaging/Yggdrasil/blob/362a6c7ded35fd56bc4655809d03fd51eb8616d8/B/boost/build_tarballs.jl#L39-L47) they are `abi=aapcs binary-format=mach-o` and the build log shows  
>   
> ```  
> Performing configuration checks  
>   
>     - default address-model    : 64-bit [1]  
>     - default architecture     : arm [1]  
> ```  
  
Shouldn't boost.build already select/set these properties? A simple ` b2 toolset=clang` on your platform should be suffice.
