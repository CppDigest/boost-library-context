# #251 Implement the fcontext asm for sparc64 [Merged]

> Username: cjeker  
> Created at: 2024-02-13 10:18:30 UTC  
> Updated at: 2024-03-10 19:18:22 UTC  
> Merged at: 2024-03-10 19:18:12 UTC  
> Closed at: 2024-03-10 19:18:12 UTC  
> Url: https://github.com/boostorg/context/pull/251  

This implements the required asm functions for sparc64_sysv_elf and was tested on an OpenBSD/sparc64 system.  
  
Jump_fcontext uses an extra C call frame to store the frame-pointer and return address. Because of this the code is simply a save (to new reg window), then forcing a window flush and finally switch stack and restore from there.  
  
Since jump_fcontext() uses a register window and stack frame, make_fcontext() reserves two call frames on the stack (one for jump_fcontext() and the other for the callback function).  
  
OpenBSD/sparc64 uses stackghost which prevents userland from overriding the return-address on the stack because of this make_fcontext() uses an extra trampoline to implement the _exit(0) call if the callback returns.  
  
All tests pass with this on OpenBSD/sparc64 (also the tests for fiber, coroutine and coroutine2).

---

## Comment 1

> Username: olk  
> Created_at: 2024-03-10 19:18:21 UTC  
> Url: https://github.com/boostorg/context/pull/251#issuecomment-1987336432  

nice - ty

---
