# #273 Convert make_fcontext etc into true C++ symbols [Merged]

> Username: rdoeffinger  
> Created at: 2024-10-14 11:28:04 UTC  
> Updated at: 2024-10-27 16:34:50 UTC  
> Merged at: 2024-10-27 16:34:44 UTC  
> Closed at: 2024-10-27 16:34:44 UTC  
> Url: https://github.com/boostorg/context/pull/273  

Also mark the non-mangled fcontext asm function symbols as hidden visibility  
These functions should not be exported as dynamic  
symbols by boost, only the namespaced C++ symbols  
introduced by this patch should.  
  
  
Note: I did not test the cmake change - I hope someone can do that more easily than me figuring it out.  
Apologies if the copyright on the new file seems a bit overkill given there is hardly any new code, but it seemed the most proper way of doing it.

---

## Comment 1

> Username: olk  
> Created_at: 2024-10-27 16:34:49 UTC  
> Url: https://github.com/boostorg/context/pull/273#issuecomment-2440090422  

ty

---
