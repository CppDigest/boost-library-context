# #398 Use forward slashes to separate include files [Merged]

> Username: georgthegreat  
> Created at: 2024-02-04 12:03:08 UTC  
> Updated at: 2026-02-06 11:15:30 UTC  
> Merged at: 2026-02-06 10:58:36 UTC  
> Closed at: 2026-02-06 10:58:36 UTC  
> Url: https://github.com/boostorg/thread/pull/398  

We use automatic dependency tracking for #includes, and wrl includes have to be tracked differently whenever we are cross-compiling the code.  
  
This PR unifies such tracking. Forward slashes work just fine on Windows.

---
