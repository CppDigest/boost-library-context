# #1 - [libs/context] make_i386_sysv_elf_gas.S:52: Error: invalid instruction suffix for `pop' [Closed]

> Username: class101  
> Created at: 2014-03-12 18:36:26 UTC  
> Updated at: 2014-07-13 18:15:42 UTC  
> Closed at: 2014-07-13 18:15:42 UTC  
> Url: https://github.com/boostorg/context/issues/1  

I fail to crosscompile the git 1.56 develop while it works on the 1.55 from a windows host targeting linux  
Compiling the 64-bit lib also worked, I think it is affecting only the linux target with address-model=32 (a similar issue I got with OSX32_64 and 1.55 that I think you have fixed, but now occurs for me on linux32 and 1.56)  
  
Command used:  
  
```  
b2 -j1 -d2 -q toolset=gcc variant=release abi=sysv address-model=32 binary-format=elf link=static,shared pch=off strip=on target-os=linux threading=multi threadapi=pthread cxxflags="-w -m32" linkflags="-w -m32" --stagedir=stage32 --with-context  
```  
  
1.56 returns (9f37c9b430fb7741ccb6834faaaf5ae060885cac)  
  
```  
Jamfile<X:\libs\boost-dev\libs\context\build>.gas bin.v2\libs\context\build\gcc-4.8.2\release\abi-sysv\address-model-32\architecture-x86\binary-format-elf\link-static\pch-off\strip-on\target-os-linux\threadap  
i-pthread\threading-multi\asm\make_i386_sysv_elf_gas.o  
  
    cpp -x assembler-with-cpp "libs\context\src\asm\make_i386_sysv_elf_gas.S" | as -am -o "bin.v2\libs\context\build\gcc-4.8.2\release\abi-sysv\address-model-32\architecture-x86\binary-format-elf\link-static\  
pch-off\strip-on\target-os-linux\threadapi-pthread\threading-multi\asm\make_i386_sysv_elf_gas.o"  
  
libs\context\src\asm\make_i386_sysv_elf_gas.S: Assembler messages:  
libs\context\src\asm\make_i386_sysv_elf_gas.S:52: Error: invalid instruction suffix for `pop'  
libs\context\src\asm\make_i386_sysv_elf_gas.S:64: Error: invalid instruction suffix for `pop'  
...failed Jamfile<X:\libs\boost-dev\libs\context\build>.gas bin.v2\libs\context\build\gcc-4.8.2\release\abi-sysv\address-model-32\architecture-x86\binary-format-elf\link-static\pch-off\strip-on\target-os-linu  
x\threadapi-pthread\threading-multi\asm\make_i386_sysv_elf_gas.o...  
...failed updating 1 target...  
...updated 23 targets...  
```  
  
1.55 final returns success  
  
As a workaround I'm changing  
  
```  
    cpp -x assembler-with-cpp "$(>)" | as -am -o "$(<)"  
```  
  
To  
  
```  
    cpp -x assembler-with-cpp "$(>)" | as --32 -am -o "$(<)"  
```  
  
But I'm unsure if that's the right thing to do in my case

---

## Comment 1

> Username: class101  
> Created at: 2014-03-12 20:26:40 UTC  
> Url: https://github.com/boostorg/context/issues/1#issuecomment-37459949  

I have the three windows hosted toolchains targeting win/linux/max ready on my googledrive let me know if you need any of them for testing purpose you are welcome
