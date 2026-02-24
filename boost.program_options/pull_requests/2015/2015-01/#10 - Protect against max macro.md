# #10 Protect against max macro [Closed]

> Username: MarcelRaad  
> Created at: 2015-01-26 13:56:38 UTC  
> Updated at: 2015-04-13 09:31:03 UTC  
> Closed at: 2015-04-13 09:28:35 UTC  
> Url: https://github.com/boostorg/program_options/pull/10  

This fixes compilation for MSVC in the case when NOMINMAX is not defined and the program_options include appears after the windows.h include.

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2015-04-09 06:17:10 UTC  
> Url: https://github.com/boostorg/program_options/pull/10#issuecomment-91125603  

Could this be merged before 6e84659 (which introduced the compile break) is merged to master please?

---

## Comment 2

> Username: vprus  
> Created_at: 2015-04-13 09:28:35 UTC  
> Url: https://github.com/boostorg/program_options/pull/10#issuecomment-92290363  

Marcel,  
  
thanks for the patch, applied now.

---
