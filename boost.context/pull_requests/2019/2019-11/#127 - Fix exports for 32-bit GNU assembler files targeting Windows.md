# #127 Fix exports for 32-bit GNU assembler files targeting Windows. [Merged]

> Username: ericastor  
> Created at: 2019-11-22 16:15:43 UTC  
> Updated at: 2019-11-22 21:39:35 UTC  
> Merged at: 2019-11-22 21:39:30 UTC  
> Closed at: 2019-11-22 21:39:30 UTC  
> Url: https://github.com/boostorg/context/pull/127  

The incorrect exports can cause linker issues (at least when using LLVM's lld-link) claiming that these primitives are not available.

---

## Comment 1

> Username: olk  
> Created_at: 2019-11-22 21:39:35 UTC  
> Url: https://github.com/boostorg/context/pull/127#issuecomment-557704459  

ty

---
