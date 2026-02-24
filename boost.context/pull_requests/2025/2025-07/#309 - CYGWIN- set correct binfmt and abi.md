# #309 [CMAKE] CYGWIN: set correct binfmt and abi [Merged]

> Username: carlo-bramini  
> Created at: 2025-07-30 11:23:37 UTC  
> Updated at: 2025-08-01 08:21:18 UTC  
> Merged at: 2025-07-31 07:49:45 UTC  
> Closed at: 2025-07-31 07:49:45 UTC  
> Url: https://github.com/boostorg/context/pull/309  

Compiling on CYGWIN prints this error:  
```  
[  4%] Building ASM-ATT object libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_sysv_elf_gas.S.o  
/usr/bin/as: unrecognized option `-x'  
make[2]: *** [libs/context/CMakeFiles/boost_context.dir/build.make:78: libs/context/CMakeFiles/boost_context.dir/src/asm/make_x86_64_sysv_elf_gas.S.o] Error 1  
make[1]: *** [CMakeFiles/Makefile2:3961: libs/context/CMakeFiles/boost_context.dir/all] Error 2  
make: *** [Makefile:156: all] Error 2  
```  
This PR fixes the right assembly sources to build because sysv and elf are nonsense on CYGWIN.  
After this patch, it correctly searches `make_x86_64_ms_pe_gas.asm`, although it still raises an error because unrecognized `-x` option.  
  
Since the changes in this patch are quite obvious, I hope that they could be included into the upcoming 1.89.0 release of boost.

---

## Comment 1

> Username: olk  
> Created_at: 2025-07-31 07:49:49 UTC  
> Url: https://github.com/boostorg/context/pull/309#issuecomment-3138907445  

ty

---
