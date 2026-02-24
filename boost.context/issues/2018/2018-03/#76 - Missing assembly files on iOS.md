# #76 - Missing assembly files on iOS [Closed]

> Username: theodelrieu  
> Created at: 2018-03-12 14:26:06 UTC  
> Updated at: 2018-11-09 13:15:03 UTC  
> Closed at: 2018-11-09 13:15:03 UTC  
> Url: https://github.com/boostorg/context/issues/76  

Hello,  
  
I had some trouble building this library on iOS (armv7/armv7s/x86/x86_64/armv8) and had to patch the build system files in order to add the relevant missing files.  
  
I found this [SO thread](https://stackoverflow.com/a/32501078) that saved me days of research, and I came up with the following patches:   
[patches.zip](https://github.com/boostorg/context/files/1803121/patches.zip)  
  
Having no notion of Boost.Build, I monkey-patched the file once per arch, and have to apply the correct patch each time (not doing it by hand though).  
  
I was thinking that you would have a better fix than me, and having it fixed upstream would be great!

---

## Comment 1

> Username: olk  
> Created at: 2018-10-28 09:34:33 UTC  
> Url: https://github.com/boostorg/context/issues/76#issuecomment-433689748  

Could you provide a pull-request, please?

---

## Comment 2

> Username: theodelrieu  
> Created at: 2018-11-06 15:06:11 UTC  
> Url: https://github.com/boostorg/context/issues/76#issuecomment-436284698  

I'd really like to, but I'm stuck at incorporating a single patch into my fork, even after reading the entire Boost.Build manual I don't understand what I'm doing wrong...  
  
Here are the changes I've made to `libs/context/build/Jamfile.v2`:  
  
```  
actions gasarm64  
{  
    cpp -x assembler-with-cpp "$(>)" | as -arch arm64 -o "$(<)"  
}  
  
alias asm_sources  
   : [ make asm/make_arm64_aapcs_macho_gas.o : asm/make_arm64_aapcs_macho_gas.S : @gasarm64 ]  
     [ make asm/jump_arm64_aapcs_macho_gas.o : asm/jump_arm64_aapcs_macho_gas.S : @gasarm64 ]  
     [ make asm/ontop_arm64_aapcs_macho_gas.o : asm/ontop_arm64_aapcs_macho_gas.S : @gasarm64 ]  
   : <abi>aapcs  
     <address-model>64  
     <architecture>arm  
     <binary-format>mach-o  
     <toolset>clang  
   ;  
```  
  
Here is the error I get (one for every assembly file):  
  
>Jamfile</Users/theo/Projects/boost/libs/context/build>.gasarm64 bin.v2/libs/context/build/asm/make_arm64_aapcs_macho_gas.o  
clang: error: no input files  
Jamfile</Users/theo/Projects/boost/libs/context/build>.gasarm64 bin.v2/libs/context/build/asm/jump_arm64_aapcs_macho_gas.o  
clang: error: no input files  
Jamfile</Users/theo/Projects/boost/libs/context/build>.gasarm64 bin.v2/libs/context/build/asm/ontop_arm64_aapcs_macho_gas.o  
clang: error: no input files  
  
If you could help me getting the first one right, I might be able to do the others in a sweep.  
  
In the SO thread I've mentioned in the first post, there is a `alias asm_context_sources`. I have no idea how it made things work on my machine, nor why simply patching `alias asm_sources` instead did not...
