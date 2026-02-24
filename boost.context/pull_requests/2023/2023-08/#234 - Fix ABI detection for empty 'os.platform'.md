# #234 Fix ABI detection for empty 'os.platform' [Merged]

> Username: iv-m  
> Created at: 2023-08-17 10:25:47 UTC  
> Updated at: 2023-08-18 04:55:58 UTC  
> Merged at: 2023-08-18 04:55:52 UTC  
> Closed at: 2023-08-18 04:55:52 UTC  
> Url: https://github.com/boostorg/context/pull/234  

`in` operator in bjam always returns true if its first argument has no elements[1]. This means that if `os.platform` is empty (not detected), the construction introduced in commit d039c8e4da79dbc76481236249e22892bfdde047 sets ABI to `aapcs` on all platforms where `os.platform` is empty, including, e.g. riscv64, and breaks build there.  
  
This commit refactors the condition to use '=' operator, to make sure that when `os.platform` is empty we get the default ABI value, and thus fixes build on riscv64.  
  
[1] https://www.boost.org/doc/libs/1_83_0/tools/build/doc/html/index.html#jam.language.flow_of_control  
  
Fixes: d039c8e4da79dbc76481236249e22892bfdde047

---

## Comment 1

> Username: iv-m  
> Created_at: 2023-08-17 10:29:28 UTC  
> Url: https://github.com/boostorg/context/pull/234#issuecomment-1682037765  

I encountered the problem when I was building boost 1.83.0 for riscv64. Here is the error message from `b2`:  
  
```  
error: No best alternative for libs/context/build/asm_sources with <abi>aapcs <address-model>64 <architecture>riscv <asynch-exceptions>off <binary-format>elf <context-impl>fcontext <coverage>off <debug-symbols>off <deduced-address-model>64   
<deduced-architecture>riscv <exception-handling>on <extern-c-nothrow>off <inlining>full <link>shared <optimization>off <os>LINUX <pch>off <preserve-test-targets>on <profiling>off <python-debugging>off <python>3.11 <relevant>abi <relevant>ad  
dress-model <relevant>architecture <relevant>binary-format <relevant>toolset <rtti>on <runtime-debugging>off <runtime-link>shared <stdlib>native <strip>off <target-os>linux <testing.execute>on <threadapi>pthread <threading>multi <toolset-gc  
c:version>13 <toolset>gcc <variant>release <vectorize>off <visibility>hidden <warnings-as-errors>off <warnings>on  
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
    no match: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>clang  
    no match: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>gcc  
    no match: <abi>n64 <address-model>64 <architecture>mips1 <binary-format>elf <threading>multi <toolset>clang  
    no match: <abi>n64 <address-model>64 <architecture>mips1 <binary-format>elf <threading>multi <toolset>gcc  
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
  
The build continued, but  produced binaries, as the  binaries lacked `ontop_fcontext`, `jump_fcontext` and `make_fcontext` implementations.

---

## Comment 2

> Username: olk  
> Created_at: 2023-08-18 04:55:57 UTC  
> Url: https://github.com/boostorg/context/pull/234#issuecomment-1683352773  

ty

---
