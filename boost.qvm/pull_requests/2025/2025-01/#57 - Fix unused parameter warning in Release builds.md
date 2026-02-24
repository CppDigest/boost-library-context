# #57 Fix unused parameter warning in Release builds [Merged]

> Username: jhasse  
> Created at: 2025-01-16 22:08:10 UTC  
> Updated at: 2025-01-20 08:59:48 UTC  
> Merged at: 2025-01-19 07:55:32 UTC  
> Closed at: 2025-01-19 07:55:32 UTC  
> Url: https://github.com/boostorg/qvm/pull/57  

BOOST_QVM_ASSERT is disabled there, so cast the variables to void instead.  
  
Otherwise a user of QVM with -Wextra (and clang, didn't happen with GCC for some reason) is spammed with lots of warnings in those headers.

---

## Comment 1

> Username: zajo  
> Created_at: 2025-01-17 18:09:15 UTC  
> Url: https://github.com/boostorg/qvm/pull/57#issuecomment-2598922854  

Thanks. Evidently the the tests are not working now, I'll have to fix this and then I'll approve.

---
