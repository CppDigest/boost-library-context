# #295 - Assembler error when trying to compile x86 code with GCC on Mac [Closed]

> Username: ritvikrao  
> Created at: 2025-03-05 15:06:19 UTC  
> Updated at: 2025-03-05 15:56:26 UTC  
> Closed at: 2025-03-05 15:56:26 UTC  
> Url: https://github.com/boostorg/context/issues/295  

I am trying to compile the Boost context library on a Mac using my own install of GCC, but I get some assembler errors when trying to compile jump_x86_64_sysv_elf_gas.S. I have a Mac with an Intel x86 cpu. Here is my command:  
  
`gcc -o jump_x86_64_sysv_elf_gas.S.o -c jump_x86_64_sysv_elf_gas.S`    
  
And here is the error output:    
  
```  
/Users/ritvik/reconverse/boost-context/jump_x86_64_sysv_elf_gas.S:44:1: error: unknown directive  
.type jump_fcontext,@function  
^  
/Users/ritvik/reconverse/boost-context/jump_x86_64_sysv_elf_gas.S:139:1: error: unknown directive  
.size jump_fcontext,.-jump_fcontext  
^  
/Users/ritvik/reconverse/boost-context/jump_x86_64_sysv_elf_gas.S:142:19: error: unexpected token in '.section' directive  
.section .note.GNU-stack,"",%progbits  
                  ^  
make[2]: *** [boost-context/CMakeFiles/boost-context.dir/jump_x86_64_sysv_elf_gas.S.o] Error 1  
make[1]: *** [boost-context/CMakeFiles/boost-context.dir/all] Error 2  
make: *** [all] Error 2  
```  
  
These errors seem to imply that the clang assembler is being used instead of the GCC assembler. I have used gcc -v to confirm that I am using gcc, not AppleClang:  
  
```  
gcc (MacPorts gcc14 14.2.0_3+stdlib_flag) 14.2.0  
Copyright (C) 2024 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
How do I ensure that I'm using the GCC assembler instead of the Clang assembler on x86? Or do I need to use one of the other boost assembly files for Mac?

---

## Comment 1

> Username: olk  
> Created at: 2025-03-05 15:56:23 UTC  
> Url: https://github.com/boostorg/context/issues/295#issuecomment-2701367212  

wrong binary-format - compile with `b2 toolset=gcc`
