# #141 - Assembler binary formats and abi [Closed]

> Username: eldiener  
> Created at: 2020-04-25 21:40:41 UTC  
> Updated at: 2020-06-26 05:16:00 UTC  
> Closed at: 2020-06-26 05:16:00 UTC  
> Url: https://github.com/boostorg/context/issues/141  

I am trying to determine what assembnler binary format and abi I should choose for the Embarcadero assembler. Before I ask about this I need to understand what your features actually stand for. I did not see any documentation about theses assembler binary format and abi choices in the docs, so maybe you can explain to what each one refers.  
  
For the binary formats I see:  
  
elf  
mach-o  
pe  
xcoff  
  
For the abi I see:  
  
aapcs  
eabi  
ms  
n32  
n64  
o32  
o64  
sysv  
x32  
  
Would you explain to what each of these refer ? It is difficult to pick out the right assembler combination without knowing what these are all about.

---

## Comment 1

> Username: olk  
> Created at: 2020-04-27 04:22:45 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-619706610  

Binary format and ABI doesn't depend on the assembler binary it is determined by what the environment/operating system uses/is compiled for.  
For instance Linux on x86_64 uses <binary-format>=elf and <abi>=sysv or on ARM <binary-format>=elf and <abi>=aapcs.

---

## Comment 2

> Username: eldiener  
> Created at: 2020-04-27 13:56:58 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-620003150  

First, I am merely asking what your mnemonics actually stand for. While you may understand what, as an example, 'n32' means how is someone else to understand it unless you explain it somewhere, preferably in either your documentation or, if you do not think that is necessary, at the top of jamfile.v2 in the context/build, directory.  
  
Second my practical problem is to choose the correct binary-format/abi in Boost.Context for the Embarcadero assemblers in order to integrate the Embarcadero clang-based compilers running on Windows into Boost.Context.   
  
For the 32-bit assembler tasm32 is used, which I was told is masm compatible. So for the 32-bit assembler in Boost.Context do I choose 'pe' as my binary format and 'ms' as my abi ? Are those the Microsoft masm settings ?  
  
For the 64-bit assembler nasm is used and I was told with the -t and -elf64 options should be passed to it, so that is what I will use when the 64-bit assembler is invoked in the embarcadero.jam file. I gather from the -elf64 switch I pass to nasm that I choose 'elf' as the binary format but I have no idea what to choose as the abi. While the 64-bit assembler produces an .o file that is compatible with clang, the 64-bit assembler does run on Windows and not Linux.

---

## Comment 3

> Username: olk  
> Created at: 2020-04-27 14:13:13 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-620013809  

'n32' etc. are the 'official' names of the ABIs (for instance MIPS has n32, n64, o32, o64 ...)  
If you look into build/Jamfile you'll see comments like 'ARM/AAPCS/ELF' telling you the architecture/abi/binary-format.  
  
As I told you the binary-format/abi does depend on for which platform you are compiling (for instance LINUX on MIPS using o32 the binary-format -> mips/sysv/o32).  
  
pe/ms are Windows specific - correct, but aren't MASM settings

---

## Comment 4

> Username: eldiener  
> Created at: 2020-04-27 17:13:30 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-620117624  

What are MASM settings ? For msvc in your context/build/Jamfile.v2 you have:  
  
alias asm_sources  
   : asm/make_i386_ms_pe_masm.asm  
     asm/jump_i386_ms_pe_masm.asm  
     asm/ontop_i386_ms_pe_masm.asm  
     dummy.cpp  
   : <abi>ms  
     <address-model>32  
     <architecture>x86  
     <binary-format>pe  
     <toolset>msvc  
   ;  
  
and   
  
alias asm_sources  
   : asm/make_x86_64_ms_pe_masm.asm  
     asm/jump_x86_64_ms_pe_masm.asm  
     asm/ontop_x86_64_ms_pe_masm.asm  
     dummy.cpp  
   : <abi>ms  
     <address-model>64  
     <architecture>x86  
     <binary-format>pe  
     <toolset>msvc  
   ;  
  
That sure looks like pe/ms to me.  
  
For the Embarcadero nasm 64-bit assembler for the x86 architecture I do see some elf binary-formats with clang as the compiler but I do not know whether my abi for nasm is x32 or sysv, when passed the -elf64 command line switch. Maybe the nasm document will tell me.

---

## Comment 5

> Username: olk  
> Created at: 2020-04-27 17:47:24 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-620134781  

Please understand that the binary-format and abi is determined by the version/Variant of operating system/platform you compile the code for. It is not determined by the variant of the assemble compiler you choose.  
For instance if you compile for x64/Windows the assembler files selected by the Jamfilefor for clang's assembler are determined by:  
  
# X86_64/MS/PE  
alias asm_sources  
   : asm/make_x86_64_ms_pe_gas.asm  
     asm/jump_x86_64_ms_pe_gas.asm  
     asm/ontop_x86_64_ms_pe_gas.asm  
     dummy.cpp  
   : <abi>ms    // ABI used by x64/Windows  
     <address-model>64 // 64bit  
     <architecture>x86 // Intel architecture  
     <binary-format>pe  // binary- format used by x64/Windows  
     <toolset>clang  // use clang's assembler  
   ;  
  
You have to exhcange the <toolset> to a certain value that identifies your Embarcadero compiler and it's assembler.  
  
Windows is unaware the ELF format (this is LINUX's default).

---

## Comment 6

> Username: eldiener  
> Created at: 2020-04-27 19:17:11 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-620180393  

I was first talking aboout the 32-bit assembler for Embarcadero, tasm32. which is masm compatible. Therefore I assumed it could use the same 32-bit assembler as msvc uses in your configuration, which would then be:  
  
alias asm_sources  
   : asm/make_i386_ms_pe_masm.asm  
     asm/jump_i386_ms_pe_masm.asm  
     asm/ontop_i386_ms_pe_masm.asm  
     dummy.cpp  
   : <abi>ms  
     <address-model>32  
     <architecture>x86  
     <binary-format>pe  
     <toolset>embarcadero  
   ;  
  
As for the 64-bit assembler, which is based on nasm with the -t and -elf64 options passed to the nasm assembler, it does produce ELF output, which is what Embarcader C++ 64-bit compiler bcc64 produces, even though it runs on Windows. Are you then saying that their is no abi on Windows that can handle this among your configurations, and that therefore I can not configure the nasm assembler producing ELF 64-bit output to use your Boost.Context library ? If that is so it is OK, but then it appears as if the Boost.Context library is unusable by bcc64 and I have to just makle a note of that. I am trying to get all Boost libraries to work with the Embarcadero 32-bit and 64-bit C++ compiler setup, but if it is not possible to do so for any particular Boost library then that is the way it is and not the end of the world <g>.

---

## Comment 7

> Username: olk  
> Created at: 2020-04-27 19:30:31 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-620187386  

I've never heard of ELF on WIndows - but I might be wrong. At least I'm wondering how a ELF binary is able to load PE (system) libray.  
  
In the Jamfile of boost.context you tell boost.build which assembler files have to be choosen for a combination of <architecture>, <abi>, <binary-format>, <address-model> and <toolset>.  
This five boost.build properties must be detected by boost.build using some macros or have to be specified at command line.  
  
Usually each assembler tool hase it's own emnonics - some mimic the memnonics of others (like nasm). But I don't know which assembler tool Embarcadero compler is using.  
  
The ABI beside of other stuff defines the calling convention, e.g. functions invokation/parameter passing as well as layout of a stack frame.  
  
The binary-format tells how the binary itself is structured.

---

## Comment 8

> Username: eldiener  
> Created at: 2020-05-12 09:10:03 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-627215593  

The object files produced by the Embarcadero C++ bcc64 compiler are in ELF format, since Embarcadero is using their own build of clang-5.0 as the compiler, but the linker then takes the object files and produces a normal Windows PE executable image and/or DLLs.  
  
I tried your X86_64/SYSV/X32 ELF assembly language files with the embarcadero toolset configured to use 'nasm -t -f elf64' but nasm did not accept the C++ style /* */ comments at the beginning of the file. Are C++ style comments allowable for that binary format ? Is there a document which shows that such comments are allowable so I can cite it when replying to the 'nasm' programmers ?

---

## Comment 9

> Username: olk  
> Created at: 2020-05-13 14:13:48 UTC  
> Updated at: 2020-05-13 14:23:27 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-628017337  

These are GAS own comment syntax (not C++ comments).  
  
Please note, that each assembler tool has its own syntax. It is more likely that assembler written for GAS differs from NASM assembler.  
You have to copy and adjust the GAS assembler code for NAMS.  
The next step would be to modify Jamfile from boost.context so that your NASM assembler is choosen:  
```  
alias asm_sources  
   : asm/make_x86_64_sysv_elf_nasm.S  
     asm/jump_x86_64_sysv_elf_nasm.S  
     asm/ontop_x86_64_sysv_elf_nasm.S  
   : <abi>x32  
     <address-model>64  
     <architecture>x86  
     <binary-format>elf  
     <toolset>embarcadero  
   ;  
```  
If boost.build is running to compile the boost libraries, the properties from above must be have these values. Alternatively these properties can be specified at command line:  
  
`b2 <address-model>=64 <architecture>=x86 <binary-format>=elf <toolset>=embarcadero`  
  
Beside `<toolset>` the other properties are determined by boost.build's macros. But sometimes you can't detect the correct property value - especially this is the case of `<binary-format>` and `<abi>`. In this case default values depending on the architecture and operating system are defined. For instance this is the case for Linux on MIPS (Linux can be build using O32 or N32 or N64...).

---

## Comment 10

> Username: eldiener  
> Created at: 2020-05-13 16:56:34 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-628118311  

>   
>   
> These are GAS own comment syntax (not C++ comments).  
>   
> Please note, that each assembler tool has its own syntax. It is more likely that assembler written for GAS differs from NASM assembler.  
> You have to copy and adjust the GAS assembler code for NAMS.  
> The next step would be to modify Jamfile from boost.context so that your NASM assembler is choosen:  
>   
> ```  
> alias asm_sources  
>    : asm/make_x86_64_sysv_elf_nasm.S  
>      asm/jump_x86_64_sysv_elf_nasm.S  
>      asm/ontop_x86_64_sysv_elf_nasm.S  
>    : <abi>x32  
>      <address-model>64  
>      <architecture>x86  
>      <binary-format>elf  
>      <toolset>embarcadero  
>    ;  
> ```  
>   
> If boost.build is running to compile the boost libraries, the properties from above must be have these values. Alternatively these properties can be specified at command line:  
>   
> `b2 <address-model>=64 <architecture>=x86 <binary-format>=elf <toolset>=embarcadero`  
>   
> Beside `<toolset>` the other properties are determined by boost.build's macros. But sometimes you can't detect the correct property value - especially this is the case of `<binary-format>` and `<abi>`. In this case default values depending on the architecture and operating system are defined. For instance this is the case for Linux on MIPS (Linux can be build using O32 or N32 or N64...).  
  
I understand what I have to do to have an asm_sources for Embarcadero and have done it and tested it. The problem is 'nasm'. Talking to 'nasm' developers on their on-line forum it turns out that 'nasm' does not understand gas at all, but only understands Intel assembly language format. Is there any chance that you can create a version of the three 'x86_64_sysv_elf_gas.S' files in Intel format ? I have no idea of the difference between gas and Intel format and have not done any assembly language programming in decades.

---

## Comment 11

> Username: eldiener  
> Created at: 2020-05-13 23:05:11 UTC  
> Url: https://github.com/boostorg/context/issues/141#issuecomment-628290751  

I solved this problem by using the bcc64 compiler with the clang/gcc command line option of "-x assembler-with-cpp", and this actually worked to assemble your 'x86_64_sysv_elf_gas.S' files. Whether your assembly code works correctly on Windows instead of Linux I do not know, but the assermbly went through without errors. I should not have followed the 'nasm' path, but an Embarcadero employeee told me that is what they use for 64-bit assembly. I guess they did not realize that their own compiler, based on clang-5.0, has the "-x assembler-with-cpp" option.
