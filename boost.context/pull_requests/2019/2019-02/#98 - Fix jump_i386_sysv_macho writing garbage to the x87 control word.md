# #98 Fix jump_i386_sysv_macho writing garbage to the x87 control word [Merged]

> Username: tux3  
> Created at: 2019-02-18 16:36:07 UTC  
> Updated at: 2019-02-18 18:31:41 UTC  
> Merged at: 2019-02-18 18:31:40 UTC  
> Closed at: 2019-02-18 18:31:40 UTC  
> Url: https://github.com/boostorg/context/pull/98  

I'm running into crashes on 32bit iOS devices caused by the x87 FPU control word not being saved correctly.  
I'm not familiar with the boost.context implementation, but it seems this line is overwriting the control word right before it is loaded.  
  
I think the macho code may have been modeled after the elf version,  
which writes the returned transport_t through a pointer in eax,  
however macho is expected to return its transport_t result in eax:edx.  
  
The macho code mistakenly wrote its "data" return value through eax.  
This happens to overwrite the saved fc_x87_cw before it is loaded,  
resulting in floating-point exceptions and crashes in unrelated code.  
  
Does that sound about right?

---

## Comment 1

> Username: olk  
> Created_at: 2019-02-18 18:31:34 UTC  
> Url: https://github.com/boostorg/context/pull/98#issuecomment-464837172  

yes, your are right (but ontop_i386_sysv_macho_gas.S is correct) -ty

---
