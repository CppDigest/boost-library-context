# #253 - ASM support for sparc64 systems [Closed]

> Username: cjeker  
> Created at: 2024-02-27 11:38:36 UTC  
> Updated at: 2024-03-10 19:21:33 UTC  
> Closed at: 2024-03-10 19:21:33 UTC  
> Url: https://github.com/boostorg/context/issues/253  

Add back proper ASM for sparc64 systems that works.  
  
This is needed to support systems without ucontext support. Also the direct ASM is probably a lot faster then the ucontext code  
since it avoids extra system calls.  
  
It would be nice if PR #251 would be reviewed.  
  
Also https://github.com/php/php-src/pull/13382 depends on this.

---

## Comment 1

> Username: olk  
> Created at: 2024-03-10 19:21:33 UTC  
> Url: https://github.com/boostorg/context/issues/253#issuecomment-1987337161  

done - ty
