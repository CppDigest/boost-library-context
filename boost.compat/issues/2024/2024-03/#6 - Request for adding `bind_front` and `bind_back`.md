# #6 - Request for adding `bind_front` and `bind_back` [Closed]

> Username: ashtum  
> Created at: 2024-03-14 10:22:31 UTC  
> Updated at: 2024-03-30 19:55:20 UTC  
> Closed at: 2024-03-30 19:55:20 UTC  
> Url: https://github.com/boostorg/compat/issues/6  

`bind_front` and `bind_back` are very handy wrappers, especially for asynchronous applications where we want to pass member functions as completion handlers. Unfortunately, their standard counterparts require C++20 and C++23, respectively.  
  
Considering their implementation, we might get `invoke` and probably `apply` as byproducts.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-03-14 10:33:49 UTC  
> Url: https://github.com/boostorg/compat/issues/6#issuecomment-1997131548  

I intend to work on these after 1.85 ships.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-03-30 19:55:20 UTC  
> Url: https://github.com/boostorg/compat/issues/6#issuecomment-2028455138  

`bind_front`, `bind_back`, `invoke` implemented on develop.
