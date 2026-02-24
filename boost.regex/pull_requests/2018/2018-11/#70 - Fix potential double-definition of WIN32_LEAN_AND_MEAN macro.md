# #70 Fix potential double-definition of WIN32_LEAN_AND_MEAN macro [Merged]

> Username: scramsby  
> Created at: 2018-11-10 00:16:32 UTC  
> Updated at: 2018-11-11 16:52:32 UTC  
> Merged at: 2018-11-11 16:52:32 UTC  
> Closed at: 2018-11-11 16:52:32 UTC  
> Url: https://github.com/boostorg/regex/pull/70  

This prevents build errors if you're building these files using a build configuration that defines this macro on the command-line.

---
