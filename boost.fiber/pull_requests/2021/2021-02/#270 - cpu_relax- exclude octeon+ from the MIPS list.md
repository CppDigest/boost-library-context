# #270 cpu_relax:  exclude octeon+ from the MIPS list [Merged]

> Username: neheb  
> Created at: 2021-02-14 02:50:43 UTC  
> Updated at: 2021-03-01 07:44:41 UTC  
> Merged at: 2021-03-01 06:52:40 UTC  
> Closed at: 2021-03-01 06:52:40 UTC  
> Url: https://github.com/boostorg/fiber/pull/270  

The pause MIPS instruction was added in version 2.6 of the standard. Unfortunately,  
octeon+ seems to be based on an older standard. Also unfortunately GCC doesn't  
have a macro for minor ISA versions.

---

## Comment 1

> Username: olk  
> Created_at: 2021-03-01 06:52:45 UTC  
> Url: https://github.com/boostorg/fiber/pull/270#issuecomment-787699527  

ty

---
