# #305 fix build of ontop_sparc64_sysv_elf_gas.S [Merged]

> Username: tkoecker  
> Created at: 2025-05-16 10:36:13 UTC  
> Updated at: 2025-05-25 14:30:55 UTC  
> Merged at: 2025-05-25 14:30:47 UTC  
> Closed at: 2025-05-25 14:30:47 UTC  
> Url: https://github.com/boostorg/context/pull/305  

Fix the symbol names used for .size.  
  
It seems that this line was copy pasted from the "jump" implementation without adjusting the symbol name.

---

## Comment 1

> Username: olk  
> Created_at: 2025-05-25 14:30:54 UTC  
> Url: https://github.com/boostorg/context/pull/305#issuecomment-2907859419  

ty

---
