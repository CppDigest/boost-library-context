# #218 Make sure the i386 sysv stack is aligned [Merged]

> Username: ndossche  
> Created at: 2023-01-25 21:11:01 UTC  
> Updated at: 2023-01-26 05:48:32 UTC  
> Merged at: 2023-01-26 05:48:26 UTC  
> Closed at: 2023-01-26 05:48:26 UTC  
> Url: https://github.com/boostorg/context/pull/218  

SysV ABI requires a stack alignment of 16 bytes. Currently, for i386 with SysV ABI, the trampoline function is entered with an unaligned stack. This causes problems for the context-function that is jumped to as its stack is also unaligned. This causes a crash for our use-case because the context function contains an SSE instruction which reads from the stack. The SSE instruction requires the correct alignment. Fix it by changing the 0x2c offset to 0x30, such that the stack remains aligned.  
  
Related issue in this repo: #210   
  
For reference:  
https://github.com/php/php-src/pull/10407#issuecomment-1404180877 and https://github.com/php/php-src/issues/10398

---

## Comment 1

> Username: olk  
> Created_at: 2023-01-26 05:48:32 UTC  
> Url: https://github.com/boostorg/context/pull/218#issuecomment-1404587085  

ty

---
