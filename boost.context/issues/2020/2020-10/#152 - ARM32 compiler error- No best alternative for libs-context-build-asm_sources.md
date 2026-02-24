# #152 - ARM32 compiler error: No best alternative for libs/context/build/asm_sources [Closed]

> Username: tangrames  
> Created at: 2020-10-30 03:49:27 UTC  
> Updated at: 2020-10-30 07:05:17 UTC  
> Closed at: 2020-10-30 07:05:16 UTC  
> Url: https://github.com/boostorg/context/issues/152  

I am trying to build boost 1.66&1.70 with arm 32 bit compiler. I am seeing the error:  
No best alternative for libs/context/build/asm_sources  
Although, after getting this error, compilation looks to succeed, but I am not sure if it did properly or not.  
  
I have this line in the project-config.jam:  
  
if ! gcc in [ feature.values <toolset> ]  
{  
    using gcc : : /opt/phytec-yogurt/BSP-Yocto-i.MX6UL-PD19.1.0/sysroots/x86_64-phytecsdk-linux/usr/bin/arm-phytec-linux-gnueabi/arm-phytec-linux-gnueabi-gcc  -march=armv7ve -marm -mfpu=neon-vfpv4 -mfloat-abi=hard -mcpu=cortex-a7 --sysroot=/opt/phytec-yogurt/BSP-Yocto-i.MX6UL-PD19.1.0/sysroots/cortexa7hf-neon-vfpv4-phytec-linux-gnueabi ;  
}  
After running b2, I get the following output:  
  
Performing configuration checks  
  
    - default address-model    : 32-bit  
    - default architecture     : arm  
  
Building the Boost C++ Libraries.  
  
  
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
    - C++11 mutex              : yes  
    - has_icu builds           : no  
    - lockfree boost::atomic_flag : yes  
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

---

## Comment 1

> Username: tangrames  
> Created at: 2020-10-30 05:19:31 UTC  
> Url: https://github.com/boostorg/context/issues/152#issuecomment-719195013  

> I am trying to build boost 1.66&1.70 with arm 32 bit compiler. I am seeing the error:  
> No best alternative for libs/context/build/asm_sources  
> Although, after getting this error, compilation looks to succeed, but I am not sure if it did properly or not.  
>   
> I have this line in the project-config.jam:  
>   
> if ! gcc in [ feature.values ]  
> {  
> using gcc : : /opt/phytec-yogurt/BSP-Yocto-i.MX6UL-PD19.1.0/sysroots/x86_64-phytecsdk-linux/usr/bin/arm-phytec-linux-gnueabi/arm-phytec-linux-gnueabi-gcc -march=armv7ve -marm -mfpu=neon-vfpv4 -mfloat-abi=hard -mcpu=cortex-a7 --sysroot=/opt/phytec-yogurt/BSP-Yocto-i.MX6UL-PD19.1.0/sysroots/cortexa7hf-neon-vfpv4-phytec-linux-gnueabi ;  
> }  
> After running b2, I get the following output:  
>   
> Performing configuration checks  
>   
> ```  
> - default address-model    : 32-bit  
> - default architecture     : arm  
> ```  
>   
> Building the Boost C++ Libraries.  
>   
> error: No best alternative for libs/context/build/asm_sources  
> next alternative: required properties: aapcs 32 arm elf multi clang  
> not matched  
> next alternative: required properties: aapcs 32 arm elf multi gcc  
> not matched  
> next alternative: required properties: aapcs 32 arm elf multi qcc  
> not matched  
> next alternative: required properties: aapcs 32 arm mach-o multi clang  
> not matched  
> next alternative: required properties: aapcs 32 arm mach-o multi darwin  
> not matched  
> next alternative: required properties: aapcs 32 arm pe multi msvc  
> not matched  
> next alternative: required properties: aapcs 64 arm elf multi clang  
> not matched  
> next alternative: required properties: aapcs 64 arm elf multi gcc  
> not matched  
> next alternative: required properties: aapcs 64 arm mach-o multi clang  
> not matched  
> next alternative: required properties: aapcs 64 arm mach-o multi darwin  
> not matched  
> next alternative: required properties: o32 32 mips1 elf multi clang  
> not matched  
> next alternative: required properties: o32 32 mips1 elf multi gcc  
> not matched  
> next alternative: required properties: n64 64 mips1 elf multi clang  
> not matched  
> next alternative: required properties: n64 64 mips1 elf multi gcc  
> not matched  
> next alternative: required properties: sysv 32 power elf multi clang  
> not matched  
> next alternative: required properties: sysv 32 power elf multi gcc  
> not matched  
> next alternative: required properties: sysv 32 power mach-o multi darwin  
> not matched  
> next alternative: required properties: sysv 32 power xcoff multi clang  
> not matched  
> next alternative: required properties: sysv 32 power xcoff multi gcc  
> not matched  
> next alternative: required properties: sysv 64 power elf multi clang  
> not matched  
> next alternative: required properties: sysv 64 power elf multi gcc  
> not matched  
> next alternative: required properties: sysv 64 power mach-o multi clang  
> not matched  
> next alternative: required properties: sysv 64 power mach-o multi darwin  
> not matched  
> next alternative: required properties: sysv 64 power xcoff multi clang  
> not matched  
> next alternative: required properties: sysv 64 power xcoff multi gcc  
> not matched  
> next alternative: required properties: sysv 32_64 power mach-o multi  
> not matched  
> next alternative: required properties: sysv 32 x86 elf multi clang  
> not matched  
> next alternative: required properties: sysv 32 x86 elf multi gcc  
> not matched  
> next alternative: required properties: sysv 32 x86 elf multi intel  
> not matched  
> next alternative: required properties: sysv 32 x86 mach-o multi clang  
> not matched  
> next alternative: required properties: sysv 32 x86 mach-o multi darwin  
> not matched  
> next alternative: required properties: ms 32 x86 pe multi clang  
> not matched  
> next alternative: required properties: ms 32 x86 pe multi clang-win  
> not matched  
> next alternative: required properties: ms 32 x86 pe multi gcc  
> not matched  
> next alternative: required properties: ms 32 x86 pe multi intel  
> not matched  
> next alternative: required properties: ms 32 x86 pe multi msvc  
> not matched  
> next alternative: required properties: sysv 64 x86 elf multi clang  
> not matched  
> next alternative: required properties: sysv 64 x86 elf multi gcc  
> not matched  
> next alternative: required properties: sysv 64 x86 elf multi intel  
> not matched  
> next alternative: required properties: sysv 64 x86 mach-o multi clang  
> not matched  
> next alternative: required properties: sysv 64 x86 mach-o multi darwin  
> not matched  
> next alternative: required properties: sysv 64 x86 mach-o multi intel  
> not matched  
> next alternative: required properties: ms 64 x86  
  
已解决

---

## Comment 2

> Username: olk  
> Created at: 2020-10-30 07:05:16 UTC  
> Url: https://github.com/boostorg/context/issues/152#issuecomment-719324105  

you can specify the properties at b2 command line:  
b2  abi=aapcs address-model=32 architecture=arm binary-format=elf ...
