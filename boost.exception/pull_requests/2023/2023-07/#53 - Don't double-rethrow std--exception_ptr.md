# #53 Don't double-rethrow std::exception_ptr [Merged]

> Username: ecatmur  
> Created at: 2023-07-28 05:35:18 UTC  
> Updated at: 2024-01-06 13:56:19 UTC  
> Merged at: 2023-08-05 01:28:03 UTC  
> Closed at: 2023-08-05 01:28:03 UTC  
> Url: https://github.com/boostorg/exception/pull/53  

It's unnecessary to throw and then rethrow std::exception_ptr, just derive clone_base separately.  
  
nb. https://gcc.gnu.org/bugzilla/show_bug.cgi?id=110835

---

## Comment 1

> Username: ecatmur  
> Created_at: 2023-07-28 07:58:03 UTC  
> Url: https://github.com/boostorg/exception/pull/53#issuecomment-1655226514  

https://github.com/llvm/llvm-project/issues/64190

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-01-06 13:56:18 UTC  
> Url: https://github.com/boostorg/exception/pull/53#issuecomment-1879694934  

This has been applied and then reverted without explanation, why?

---
