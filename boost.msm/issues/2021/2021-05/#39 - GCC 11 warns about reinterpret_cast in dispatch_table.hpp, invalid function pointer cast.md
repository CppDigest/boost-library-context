# #39 - GCC 11 warns about reinterpret_cast in dispatch_table.hpp, invalid function pointer cast [Closed]

> Username: mgaunard  
> Created at: 2021-05-15 00:36:23 UTC  
> Updated at: 2024-02-17 14:44:27 UTC  
> Closed at: 2024-02-17 14:44:27 UTC  
> Url: https://github.com/boostorg/msm/issues/39  

This can be worked around by doing a reinterpret_cast through uintptr_t first.

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-17 14:44:27 UTC  
> Url: https://github.com/boostorg/msm/issues/39#issuecomment-1950226839  

interesting solution. Thanks.
