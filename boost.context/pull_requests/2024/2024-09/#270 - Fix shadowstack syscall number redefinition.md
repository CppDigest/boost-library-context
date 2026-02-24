# #270 Fix shadowstack syscall number redefinition [Merged]

> Username: Lastique  
> Created at: 2024-09-12 16:55:24 UTC  
> Updated at: 2024-12-26 08:40:31 UTC  
> Merged at: 2024-12-26 07:09:06 UTC  
> Closed at: 2024-12-26 07:09:07 UTC  
> Url: https://github.com/boostorg/context/pull/270  

`__NR_map_shadow_stack` is defined to 453 (not 451) on Ubuntu 24.04 with Linux kernel 6.8.0-44, gcc 13.2.0 and glibc 2.39. This causes warnings about macro redefinition when Boost.Fiber is compiled.  
      
Change the syscall number to 453 and only define the macro if it has not been defined already. Also include `unistd.h` for `syscall()` and `__NR_*` constants.  
  
Also convert tabs to spaces and trim trailing spaces.  
  
Fixes https://github.com/boostorg/context/issues/263.  
Fixes https://github.com/boostorg/context/issues/269.

---

## Comment 1

> Username: olk  
> Created_at: 2024-12-26 07:09:12 UTC  
> Url: https://github.com/boostorg/context/pull/270#issuecomment-2562237008  

ty

---
