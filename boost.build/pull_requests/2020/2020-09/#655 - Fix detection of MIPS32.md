# #655 Fix detection of MIPS32 [Merged]

> Username: iv-m  
> Created at: 2020-09-10 14:27:02 UTC  
> Updated at: 2021-10-02 21:01:35 UTC  
> Merged at: 2020-09-11 13:58:03 UTC  
> Closed at: 2020-09-11 13:58:03 UTC  
> Url: https://github.com/boostorg/build/pull/655  

_ABI64 and _ABIO32 are always defined on every  
Linux MIPS system, at least if it's glibc-based:  
  
https://sourceware.org/git/?p=glibc.git;a=blob;f=sysdeps/mips/sgidefs.h;h=c9e00c6da53b1a13d83b4056f01ef5c30efe76ab  
  
The correct way to use them is to compare them  
against _MIPS_SIM symbol provided by the compiler.  
  
With this change, engine correctly detects 32-bit MIPS systems.

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:34 UTC  
> Url: https://github.com/boostorg/build/pull/655#issuecomment-932819933  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
