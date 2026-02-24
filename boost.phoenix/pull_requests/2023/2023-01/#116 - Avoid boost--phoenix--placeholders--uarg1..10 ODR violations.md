# #116 Avoid boost::phoenix::placeholders::uarg1..10 ODR violations [Closed]

> Username: stbergmann  
> Created at: 2023-01-04 16:36:10 UTC  
> Updated at: 2023-12-20 21:28:20 UTC  
> Closed at: 2023-12-20 21:28:19 UTC  
> Url: https://github.com/boostorg/phoenix/pull/116  

Those variables, defined in an include file, had external linkage, causing ODR violations.  Make them const to implicitly give them internal linkage.

---

## Comment 1

> Username: stbergmann  
> Created_at: 2023-12-20 21:28:19 UTC  
> Url: https://github.com/boostorg/phoenix/pull/116#issuecomment-1865163406  

seen addressed meanwhile with <https://github.com/boostorg/phoenix/commit/665047aac26ad4d96b266d87504b3a88ad21b37e> "avoid ODR by making this const"

---
