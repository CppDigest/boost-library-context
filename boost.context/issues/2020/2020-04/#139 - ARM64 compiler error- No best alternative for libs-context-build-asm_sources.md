# #139 - ARM64 compiler error: No best alternative for libs/context/build/asm_sources [Closed]

> Username: sbrahul  
> Created at: 2020-04-24 09:52:51 UTC  
> Updated at: 2024-02-16 19:18:10 UTC  
> Closed at: 2021-06-27 07:20:58 UTC  
> Url: https://github.com/boostorg/context/issues/139  

Hi,  
  
I am trying to build boost 1.72 with arm 64 bit compiler. I am seeing the error:  
No best alternative for libs/context/build/asm_sources  
Although, after getting this error, compilation looks to succeed, but I am not sure if it did properly or not.  
  
I have this line in the project-config.jam:  
using gcc : arm : aarch64-fsl-linux-g++ --sysroot=/sysroots/aarch64-fsl-linux ;  
  
After running b2, I get the following output:  
./b2 toolset=gcc-arm  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : arm  
  
Building the Boost C++ Libraries.  
  
  
error: No best alternative for libs/context/build/asm_sources  
    next alternative: required properties: <abi>aapcs <address-model>32  
<architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32  
<architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32  
<architecture>arm <binary-format>elf <threading>multi <toolset>qcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32  
<architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32  
<architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32  
<architecture>arm <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64  
<architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64  
<architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64  
<architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64  
<architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
...

---

## Comment 1

> Username: jschueller  
> Created at: 2020-04-24 09:57:46 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-618919191  

It's a bit similar to https://github.com/boostorg/context/issues/137

---

## Comment 2

> Username: olk  
> Created at: 2020-04-24 10:09:24 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-618924342  

I guess that gcc-arm will not match the toolset property.

---

## Comment 3

> Username: sbrahul  
> Created at: 2020-04-24 10:20:36 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-618929124  

> I guess that gcc-arm will not match the toolset property.  
  
I actually used gcc-arm as per the suggestion of the boost documentation page (although slightly older):  
https://www.boost.org/doc/libs/1_64_0/doc/html/bbv2/tasks.html  
  
The compiler I am using is gcc 9.2.0.

---

## Comment 4

> Username: olk  
> Created at: 2020-06-28 15:08:47 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-650776915  

I guess you have to specify the abi=aapcs64 and the binary-format=elf at command line.

---

## Comment 5

> Username: olk  
> Created at: 2021-06-01 15:35:21 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-852223962  

any update? may I close this issue

---

## Comment 6

> Username: lukaszkulas  
> Created at: 2022-12-01 17:58:45 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1334142990  

I had the same issue and after setting abi=aapcs64 and the binary-format=elf, I'm getting this:  
  
![image](https://user-images.githubusercontent.com/112875784/205126161-70d0b417-46f2-459d-9627-588d95ca38c6.png)  
  
@olk , do you know how to fix it?

---

## Comment 7

> Username: brechtsanders  
> Created at: 2023-02-06 20:36:31 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1419716030  

I'm also getting `error: No best alternative for libs/context/build/asm_sources` when building for Windows on ARM 64 (aarch64-w64-mingw32).

---

## Comment 8

> Username: moshev  
> Created at: 2023-11-03 14:31:27 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1792541557  

I ran into this issue as well and solved it by simply specifying everything:  
`./b2 abi=aapcs address-model=64 architecture=arm binary-format=elf <rest of options>`

---

## Comment 9

> Username: brechtsanders  
> Created at: 2023-11-03 16:49:31 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1792796252  

@moshev `binary-format=elf` doesn't sound right for Windows on ARM as it uses COFF/PE, not ELF.

---

## Comment 10

> Username: moshev  
> Created at: 2023-11-06 21:03:42 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1796430137  

@brechtsanders I was compiling for Linux (and AFAIU so is the OP), so ELF is the right option for me. Of course if you're compiling for Windows, use the correct format for it.

---

## Comment 11

> Username: brechtsanders  
> Created at: 2023-11-07 19:01:24 UTC  
> Updated at: 2023-11-07 19:15:29 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1799586627  

@moshev Ok. I suppose I also need to stick with `abi=ms` then instead of `abi=aapcs`?

---

## Comment 12

> Username: cryptozoidberg  
> Created at: 2024-02-15 04:38:57 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1945350285  

@brechtsanders did you ever solved this issue? I'm also on Windows Arm64, facing the same issue. Any suggestions would be appreciated!

---

## Comment 13

> Username: brechtsanders  
> Created at: 2024-02-16 17:20:45 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1948939384  

My build recipe is at https://github.com/brechtsanders/winlibs_recipes/blob/main/recipes/boost.winlib  
  
Basically for boost 1.82.0 I used `--without-coroutine --without-context context-impl=winfib` for the aarch64 Windows on ARM platform.  
  
For 1.84.0 I use CMake (+Ninja) to build using the following flags `-DBOOST_CONTEXT_IMPLEMENTATION:STRING=winfib -DBOOST_CONTEXT_ABI:STRING=ms` but unfortunately the shared build still gives me errors.

---

## Comment 14

> Username: cryptozoidberg  
> Created at: 2024-02-16 18:44:55 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1949099833  

> My build recipe is at https://github.com/brechtsanders/winlibs_recipes/blob/main/recipes/boost.winlib  
>   
> Basically for boost 1.82.0 I used `--without-coroutine --without-context context-impl=winfib` for the aarch64 Windows on ARM platform.  
>   
> For 1.84.0 I use CMake (+Ninja) to build using the following flags `-DBOOST_CONTEXT_IMPLEMENTATION:STRING=winfib -DBOOST_CONTEXT_ABI:STRING=ms` but unfortunately the shared build still gives me errors.  
  
Thank you for quick response and for the link!   
Just to confirm, boost static libraries were built ok?

---

## Comment 15

> Username: brechtsanders  
> Created at: 2024-02-16 19:18:09 UTC  
> Url: https://github.com/boostorg/context/issues/139#issuecomment-1949170691  

Yes, I was able to build 1.84.0 static libraries on Windows on ARM.
