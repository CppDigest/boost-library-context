# #425 Pass alignment to holder::allocate [Closed]

> Username: vslashg  
> Created at: 2023-11-13 19:09:23 UTC  
> Updated at: 2024-09-22 16:28:54 UTC  
> Closed at: 2024-09-22 16:28:54 UTC  
> Url: https://github.com/boostorg/python/pull/425  

aca3c8 added an alignment argument to holder::allocate which defaults to 1.  However, make_constructor.hpp uses this function to create storage for a pointer and was never changed to pass an alignment, so it uses the too-permissive default.  
  
This causes unaligned pointers to be created on most architectures (although the x86 family tolerates it).  This can be detected as a failure by running projects under Clang ubsan.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2024-09-22 16:28:54 UTC  
> Url: https://github.com/boostorg/python/pull/425#issuecomment-2366862027  

This fix is incorporated via #392

---
