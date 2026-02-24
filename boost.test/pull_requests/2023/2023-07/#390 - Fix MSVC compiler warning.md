# #390 Fix MSVC compiler warning [Merged]

> Username: MarcelRaad  
> Created at: 2023-07-18 10:20:26 UTC  
> Updated at: 2023-08-04 13:45:06 UTC  
> Merged at: 2023-08-04 13:15:32 UTC  
> Closed at: 2023-08-04 13:15:32 UTC  
> Url: https://github.com/boostorg/test/pull/390  

Remove `std::move` on a temporary object to fix the following level 4 warning with Visual Studio 17.4 and later:  
  
warning C5263: calling 'std::move' on a temporary object prevents copy elision

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2023-08-04 13:15:15 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/test/pull/390#pullrequestreview-1562851319  

LGTM. Thanks

---
