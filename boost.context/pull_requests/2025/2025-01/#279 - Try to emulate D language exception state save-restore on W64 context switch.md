# #279 Try to emulate D language exception state save/restore on W64 context switch. [Open]

> Username: nat-goodspeed  
> Created at: 2025-01-02 01:27:58 UTC  
> Updated at: 2025-01-02 01:27:59 UTC  
> Url: https://github.com/boostorg/context/pull/279  

[This hint about the D language fiber implementation](https://stackoverflow.com/a/13328160) points to [this tactic for 32-bit Windows](https://github.com/dlang/druntime/blob/c39de42dd11311844c0ef90953aa65f333ea55ab/src/core/thread.d#L4023-L4091), and apparently to a [simpler tactic for 64-bit Windows](https://github.com/dlang/druntime/blob/c39de42dd11311844c0ef90953aa65f333ea55ab/src/core/thread.d#L4092-L4113).  
  
Specifically, it appears that for 64-bit Windows, the D fiber implementation [saves and restores a three-pointer block at `GS:[0]` on every context switch](https://github.com/dlang/druntime/blob/c39de42dd11311844c0ef90953aa65f333ea55ab/src/core/thread.d#L3274-L3312). The root block is initialized to contain  
  
* `0xFFFF'FFFF'FFFF'FFFF` (next block in chain, sentinel value)  
* the high end of the stack and   
* the low end of the stack  
  
respectively.  
  
This PR attempts to adopt the same tactic for the Windows 64 fcontext implementation of Boost.Context.  
  
Sadly, with the Boost.Context built from this PR, [these](https://github.com/secondlife/3p-boost/blob/nat/exstate/tests/early_exc_destroy.cpp) [three](https://github.com/secondlife/3p-boost/blob/nat/exstate/tests/fccurrexc.cpp) [programs](https://github.com/secondlife/3p-boost/blob/nat/exstate/tests/nullary_throw.cpp) that switch context during exception handling behave no differently than with unmodified Boost.Context.

---
