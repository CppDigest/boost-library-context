# #39 added Drone CI support [Merged]

> Username: joaquintides  
> Created at: 2025-09-16 16:26:11 UTC  
> Updated at: 2025-09-17 07:12:54 UTC  
> Merged at: 2025-09-16 21:31:06 UTC  
> Closed at: 2025-09-16 21:31:06 UTC  
> Url: https://github.com/boostorg/openmethod/pull/39  

Added support for Drone CI. Some notes:  
  
* Compilers tested include GCC 8 to 9 and Clang 7 to 11, which are _not_ part of GHA CI (`.github/workflows/ci.yml`). Even though tests pass for all of them, you may want to remove some if you're not commiting to supporting them.  
* MSVC 14.2 and 14.3 with `/std:c++17` do not pass the following tests:  
```  
compile_fail_bad_virtual_parameter  
compile_fail_virtual_ptr_different_registries  
```  
that is, they compile without errors when they shouldn't. GHA CI does not have this problem for MSVC 14.2, but it does for MSVC 14.3. I don't know the reason for the discrepancy with MSVC 14.2. I left this as is pending your eventual investigation.

---

## Review 1 [Approved]

> Username: jll63  
> Created_at: 2025-09-16 21:30:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/openmethod/pull/39#pullrequestreview-3231769617  

Thanks!  
  
We understand the reasons for CI failures and we are not production yet, so I'll approve and work on this later.

---
