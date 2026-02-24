# #401 Fix lstat for VxWorks 6 [Closed]

> Username: Bruflot  
> Created at: 2021-09-05 14:14:52 UTC  
> Updated at: 2025-11-29 14:53:47 UTC  
> Closed at: 2025-11-29 14:53:47 UTC  
> Url: https://github.com/boostorg/config/pull/401  

Boost does not currently compile on VxWorks 6. The parameters for the `lstat` wrapper does not have proper types.

---
