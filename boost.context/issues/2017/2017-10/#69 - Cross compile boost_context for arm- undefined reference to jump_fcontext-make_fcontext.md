# #69 - Cross compile boost_context for arm: undefined reference to jump_fcontext/make_fcontext [Closed]

> Username: bwijen  
> Created at: 2017-10-13 09:52:32 UTC  
> Updated at: 2017-10-14 13:56:46 UTC  
> Closed at: 2017-10-13 13:19:44 UTC  
> Url: https://github.com/boostorg/context/issues/69  

Created my user-config.jam as per: http://www.boost.org/build/doc/html/bbv2/tasks/crosscompile.html  
  
My user-config.jam:  
` using gcc : arm : arm-linux-gnueabihf-g++ ;`  
  
Building with `b2 toolset=gcc-arm` gives me:  
  
```  
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
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
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
  
Any ideas on how to get this working?

---

## Comment 1

> Username: bwijen  
> Created at: 2017-10-13 12:07:07 UTC  
> Url: https://github.com/boostorg/context/issues/69#issuecomment-336434314  

Can anyone confirm if my patch is correct?  
If so I will open a pull request...

---

## Comment 2

> Username: olk  
> Created at: 2017-10-13 13:19:44 UTC  
> Url: https://github.com/boostorg/context/issues/69#issuecomment-336450239  

ARM requries AAPCS ABI - you ava to specify correct b2 properties <abi>, <address-model>, <architecture>, <binary-format>.

---

## Comment 3

> Username: olk  
> Created at: 2017-10-13 13:22:01 UTC  
> Url: https://github.com/boostorg/context/issues/69#issuecomment-336450786  

your patch is wrong - ARM does not follow the SYSV ABI and so no assembler impl is provided for this combination

---

## Comment 4

> Username: bwijen  
> Created at: 2017-10-14 10:13:23 UTC  
> Url: https://github.com/boostorg/context/issues/69#issuecomment-336625290  

Hi Oliver,  
  
I know aapcs!=sysv, but for some reason `local rule default_abi` (in Jamfile.v2) returns sysv.  
(I got the idea for the patch from: https://lists.boost.org/Archives/boost/2015/02/220155.php)  
  
Do you have any idea on how to specify aapcs abi in my user-config.jam?  
  
I'm also willing to help rewriting the default_abi rule, but I have no idea where to start...

---

## Comment 5

> Username: olk  
> Created at: 2017-10-14 13:56:46 UTC  
> Url: https://github.com/boostorg/context/issues/69#issuecomment-336636468  

This must be an issue of boost.build/bjam ... I'm not familiar with it   
On b2 command line you can specify <abi>aapcs <binary-format>elf <architecture>arm <address-model>64 ... but I don't know the syntax for user-config.jam looks like.
