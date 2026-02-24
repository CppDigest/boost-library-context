# #276 cpu_relax: fixup MIPS check for 64-bit [Merged]

> Username: neheb  
> Created at: 2021-04-11 08:32:14 UTC  
> Updated at: 2021-04-11 19:38:46 UTC  
> Merged at: 2021-04-11 17:37:51 UTC  
> Closed at: 2021-04-11 17:37:51 UTC  
> Url: https://github.com/boostorg/fiber/pull/276  

It turns out that this is also broken on mips64el. Further testing  
reveals that  
  
-mips32r2 -mtune=mips32r2 -mabi=32  
  
compiles the PAUSE instruction just fine whereas  
  
-mips64r2 -mtune=mips64r2 -mabi=64  
  
does not. The PAUSE instruction was introduced in version 2.6 of the  
MIPS ISA and GCC for some reason does not allow usage of it with MIPS64.  
Modify the macro to fix the situation instead of just matching on  
octeon, which is not quite correct.

---

## Comment 1

> Username: olk  
> Created_at: 2021-04-11 17:37:57 UTC  
> Url: https://github.com/boostorg/fiber/pull/276#issuecomment-817343877  

ty

---
