# #2 Prefix references to namespace `std` with :: [Closed]

> Username: akrzemi1  
> Created at: 2021-03-22 05:26:26 UTC  
> Updated at: 2021-03-22 14:35:50 UTC  
> Closed at: 2021-03-22 14:35:50 UTC  
> Url: https://github.com/boostorg/lambda2/pull/2  

Just in case the user has a nested namespace `std` that could ADL-interact with your implementation.

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-03-22 13:55:11 UTC  
> Url: https://github.com/boostorg/lambda2/pull/2#issuecomment-804080474  

A qualified identifier does not invoke ADL and cannot ADL-interact.

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2021-03-22 14:35:50 UTC  
> Url: https://github.com/boostorg/lambda2/pull/2#issuecomment-804111974  

Right. Sorry.

---
