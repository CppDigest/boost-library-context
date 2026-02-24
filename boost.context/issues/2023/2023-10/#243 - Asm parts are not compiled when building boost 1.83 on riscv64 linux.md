# #243 - Asm parts are not compiled when building boost 1.83 on riscv64 linux [Closed]

> Username: kxxt  
> Created at: 2023-10-20 02:14:23 UTC  
> Updated at: 2023-10-23 07:28:44 UTC  
> Closed at: 2023-10-22 07:23:19 UTC  
> Url: https://github.com/boostorg/context/issues/243  

Hi,  
  
I found that `jump_fcontext`, `make_fcontext` and `ontop_fcontext` is missing in our builds on riscv64 linux.  
  
It seems the following non-fatal error when building boost is the cause for this:  
  
```  
error: No best alternative for libs/context/build/asm_sources with <abi>aapcs <address-model>64 <architecture>riscv <asynch-exceptions>off <binary-format>elf <context-impl>fcontext <coverage>off <debug-symbols>off <deduced-address-model>64 <deduced-architecture>riscv <exception-handling>on <extern-c-nothrow>off <inlining>full <link>static <optimization>speed <os>LINUX <pch>on <preserve-test-targets>on <profiling>off <python-debugging>off <python>3.11 <relevant>abi <relevant>address-model <relevant>architecture <relevant>binary-format <relevant>toolset <rtti>on <runtime-debugging>off <runtime-link>shared <stdlib>native <strip>off <target-os>linux <testing.execute>on <threadapi>pthread <threading>multi <toolset-gcc:version>13 <toolset>gcc <variant>release <vectorize>off <visibility>hidden <warnings-as-errors>off <warnings>on  
no match: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>qcc  
no match: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
no match: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
no match: <abi>aapcs <address-model>32 <architecture>arm <binary-format>pe <threading>multi <toolset>msvc  
no match: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
no match: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
no match: <abi>aapcs <address-model>64 <architecture>arm <binary-format>pe <threading>multi <toolset>msvc  
no match: <abi>sysv <address-model>64 <architecture>loongarch <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>o32 <address-model>32 <architecture>mips <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>o32 <address-model>32 <architecture>mips <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>n64 <address-model>64 <architecture>mips <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>n64 <address-model>64 <architecture>mips <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>32 <architecture>power <binary-format>mach-o <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>32 <architecture>power <binary-format>mach-o <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>32 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
no match: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
no match: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>32_64 <architecture>power <binary-format>mach-o <threading>multi  
no match: <abi>sysv <address-model>64 <architecture>riscv <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>64 <architecture>riscv <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>64 <architecture>s390x <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>64 <architecture>s390x <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
no match: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
no match: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
no match: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
no match: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
no match: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
no match: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
no match: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>embarcadero  
no match: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>borland  
no match: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
no match: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
no match: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>gcc  
no match: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
no match: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>intel  
no match: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
no match: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
no match: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
no match: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
no match: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
no match: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>embarcadero  
no match: <abi>x32 <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
no match: <abi>x32 <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
no match: <abi>x32 <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
no match: <abi>sysv <address-model>32_64 <architecture>x86 <binary-format>mach-o <threading>multi  
no match: <abi>sysv <architecture>arm+x86 <binary-format>mach-o <threading>multi  
```  
  
The abi value is wrong. And I figured out that it is caused by  `OSPLAT` not defined for riscv in b2.  
  
I will open an issue and PR for [bfgroup/b2](https://github.com/bfgroup/b2). But it seems that boost itself is using [boostorg/build](https://github.com/boostorg/build) which doesn't accept PRs now.  
  
I don't know if a PR to [bfgroup/b2](https://github.com/bfgroup/b2) will fix this issue in the future so I opened this issue here.

---

## Comment 1

> Username: olk  
> Created at: 2023-10-22 07:23:13 UTC  
> Url: https://github.com/boostorg/context/issues/243#issuecomment-1774018601  

wrong  API selection during build

---

## Comment 2

> Username: kxxt  
> Created at: 2023-10-22 15:31:57 UTC  
> Url: https://github.com/boostorg/context/issues/243#issuecomment-1774124505  

> wrong API selection during build  
  
The wrong API selection is made by the build script of this repository (`build/Jamfile.v2`).

---

## Comment 3

> Username: olk  
> Created at: 2023-10-22 18:46:43 UTC  
> Updated at: 2023-10-22 18:47:06 UTC  
> Url: https://github.com/boostorg/context/issues/243#issuecomment-1774169394  

If you look into Jamfile you see that sysv is the default for the ABI. It seams that something is wrong with your  environment.

---

## Comment 4

> Username: kxxt  
> Created at: 2023-10-23 00:18:25 UTC  
> Url: https://github.com/boostorg/context/issues/243#issuecomment-1774245224  

> If you look into Jamfile you see that sysv is the default for the ABI. It seams that something is wrong with your environment.  
  
Yes, `sysv` is the default for the ABI.   
  
`os.platform`(which is actually `OSPLAT` in the end) is not defined for riscv64 in b2.  
I am not familiar with the syntax of the Jamfile so I don't know why the build script end up in the ARM ARM64 case.  
  
This makes the boost context lib 1.83 broken on almost every riscv linux distro that ships boost 1.83.  
  
```  
dpkg-deb -x libboost-context1.83.0_1.83.0-1_riscv64.deb ./  
nm -gDC usr/lib/riscv64-linux-gnu/libboost_context.so.1.83.0  
w __cxa_finalize@GLIBC_2.27  
U __cxa_guard_acquire@CXXABI_1.3  
U __cxa_guard_release@CXXABI_1.3  
U getrlimit@GLIBC_2.27  
w _ITM_deregisterTMCloneTable  
w _ITM_registerTMCloneTable  
U __stack_chk_fail@GLIBC_2.27  
U __stack_chk_guard@GLIBC_2.27  
U sysconf@GLIBC_2.27  
000000000000092e T boost::context::stack_traits::default_size()  
000000000000087a T boost::context::stack_traits::is_unbounded()  
000000000000093a T boost::context::stack_traits::maximum_size()  
0000000000000934 T boost::context::stack_traits::minimum_size()  
00000000000008de T boost::context::stack_traits::page_size()  
# jump_fcontext, make_fcontext and ontop_fcontext is missing   
```  
  
e.g.  
  
- Arch Linux riscv: [boost-libs](https://archriscv.felixc.at/repo/extra/boost-libs-1.83.0-2.1-riscv64.pkg.tar.zst), which I have already fixed by patching b2  
- Debian: https://ftp.debian.org/debian/pool/main/b/boost1.83/libboost-context1.83.0_1.83.0-1_riscv64.deb  
  
If jump_fcontext, make_fcontext and ontop_fcontext is vital to this lib, we can make the error fatal, that is, aborting the build instead of producing a broken lib silently.

---

## Comment 5

> Username: olk  
> Created at: 2023-10-23 06:31:41 UTC  
> Updated at: 2023-10-23 06:35:47 UTC  
> Url: https://github.com/boostorg/context/issues/243#issuecomment-1774516880  

It seams to be a problem of boost build system (os property) and has to be fixed in tools/build/src/engine/jam.h.  
We need something similiar to:  
```  
#if defined( __riscv ) ||  defined( __riscv__ )  
  #if __riscv_xlen == 64  
    #define OSPLAT "OSPLAT=RISCV64"  
  #elif __riscv_xlen == 32  
    #define OSPLAT "OSPLAT=RISCV32"  
  #endif  
#endif  
```  
I've no access to RISC systems ... could you evaluate if other other preprocessor definitions beside `__riscv` and `__riscv__` exist?!  
I could provide a merge request for boost.build.

---

## Comment 6

> Username: kxxt  
> Created at: 2023-10-23 06:55:59 UTC  
> Url: https://github.com/boostorg/context/issues/243#issuecomment-1774543983  

> It seams to be a problem of boost build system (os property) and has to be fixed in tools/build/src/engine/jam.h. We need something similiar to:  
>   
> ```  
> #if defined( __riscv ) ||  defined( __riscv__ )  
>   #if __riscv_xlen == 64  
>     #define OSPLAT "OSPLAT=RISCV64"  
>   #elif __riscv_xlen == 32  
>     #define OSPLAT "OSPLAT=RISCV32"  
>   #endif  
> #endif  
> ```  
> I've no access to RISC systems ... could you evaluate if other other preprocessor definitions beside `__riscv` and `__riscv__` exist?!   
  
`__riscv_xlen` exists: https://github.com/riscv-non-isa/riscv-toolchain-conventions#cc-preprocessor-definitions  
  
I have already referenced this issue from https://github.com/bfgroup/b2/pull/341 and https://github.com/bfgroup/b2/issues/340.  And I can confirm my patch fixes this issue.    
  
Maybe the detection logic in Jamfile could be made better as well.  
  
> I could provide a merge request for boost.build.  
  
Thanks very much. It seems that [boost.build ](https://github.com/boostorg/build) no longer accepts PRs so I sent my PR to bfgroup/b2. It seems that boost itself is still using boost/build instead of bfgroup/b2.

---

## Comment 7

> Username: olk  
> Created at: 2023-10-23 07:20:45 UTC  
> Updated at: 2023-10-23 07:28:44 UTC  
> Url: https://github.com/boostorg/context/issues/243#issuecomment-1774574994  

It is upto the bfgroup to merge the merge-request ...
