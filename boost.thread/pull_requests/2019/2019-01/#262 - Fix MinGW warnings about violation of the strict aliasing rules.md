# #262 Fix MinGW warnings about violation of the strict aliasing rules [Merged]

> Username: Lastique  
> Created at: 2019-01-03 16:52:53 UTC  
> Updated at: 2019-01-20 11:31:25 UTC  
> Merged at: 2019-01-03 18:01:43 UTC  
> Closed at: 2019-01-03 18:01:43 UTC  
> Url: https://github.com/boostorg/thread/pull/262  

Use `memcpy` to convert between long and the bitfield structure and use `memcmp` to compare the two bitfields. Compilers should be smart enough to optimize away these string operations and generate virtually the same code as before.

---

## Comment 1

> Username: viboes  
> Created_at: 2019-01-03 18:01:38 UTC  
> Url: https://github.com/boostorg/thread/pull/262#issuecomment-451225912  

Thaks for the PR.

---
