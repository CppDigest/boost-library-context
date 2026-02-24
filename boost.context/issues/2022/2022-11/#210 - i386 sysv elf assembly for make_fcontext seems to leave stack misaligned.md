# #210 - i386 sysv elf assembly for make_fcontext seems to leave stack misaligned [Closed]

> Username: alexfusco5  
> Created at: 2022-11-11 19:06:37 UTC  
> Updated at: 2023-01-26 05:55:51 UTC  
> Closed at: 2023-01-26 05:55:50 UTC  
> Url: https://github.com/boostorg/context/issues/210  

In [this file](https://github.com/boostorg/context/blob/develop/src/asm/make_i386_sysv_elf_gas.S#L44), it seems to be leaving the resulting context stack misaligned.  I'm fairly rusty with x86 assembly so I don't have a great handle on why this is the case, but it seems that the offset on line 44 should be 0x34 rather than 0x2c.  The stack otherwise appears to be aligned improperly

---

## Comment 1

> Username: olk  
> Created at: 2023-01-26 05:55:50 UTC  
> Url: https://github.com/boostorg/context/issues/210#issuecomment-1404590086  

ty
