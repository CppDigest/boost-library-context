# #239 Do not memset(0) POD types [Merged]

> Username: dangelog  
> Created at: 2023-01-27 11:43:45 UTC  
> Updated at: 2023-02-13 07:54:18 UTC  
> Merged at: 2023-02-13 07:54:03 UTC  
> Closed at: 2023-02-13 07:54:03 UTC  
> Url: https://github.com/boostorg/container/pull/239  

"POD" is the wrong type trait to determine if something can be safely zero-filled in order to achieve zero initialization. Consider a type like  
  
```  
  struct POD { int POD::*ptr; };  
```  
  
This is a POD; its value initialization needs to value initialize the member, and since it's a pointer, that's zero initialization, and that's setting the pointer to null.  
  
On Itanium, a null pointer to data member is not zero filled; it actually has the value -1u.  
  
Hence, zero-filling via memset(0) a POD object like the one above is erroneous. Unfortunately there is no type trait in C++ that we can use to know if a given datatype can be value initialized by zero-filling -- we can check for trivial constructability, but that's a necessary condition, not a sufficient one (POD above is also trivially constructible).  
  
Fixes #238

---

## Comment 1

> Username: dangelog  
> Created_at: 2023-02-02 10:47:21 UTC  
> Updated_at: 2023-02-02 10:47:49 UTC  
> Url: https://github.com/boostorg/container/pull/239#issuecomment-1413536317  

Thanks for running a CI precheck. I think I have fixed the two issues found,  
1) there's a run in C++03 mode, so I can't create a vector of a local type. Moved the type outside main().  
2) MSVC [miscompiles](https://developercommunity.visualstudio.com/t/Pointer-to-data-member-is-not-initialize/10238905) the code, so the test is skipped there.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2023-02-13 07:54:18 UTC  
> Url: https://github.com/boostorg/container/pull/239#issuecomment-1427493203  

Many thanks for the report and the patch!

---
