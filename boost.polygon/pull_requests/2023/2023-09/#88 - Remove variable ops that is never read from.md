# #88 Remove variable ops that is never read from. [Closed]

> Username: tinko92  
> Created at: 2023-09-29 21:08:58 UTC  
> Updated at: 2025-03-18 10:06:08 UTC  
> Closed at: 2025-03-18 10:06:08 UTC  
> Url: https://github.com/boostorg/polygon/pull/88  

This removes an unused variable to fix a warning with "-Wunused-but-set-variable" when compiling with Clang.

---
