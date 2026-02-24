# #622 - Warnings in 32-bit msvc builds [Closed]

> Username: vinniefalco  
> Created at: 2022-11-07 23:12:00 UTC  
> Updated at: 2022-11-09 23:08:39 UTC  
> Closed at: 2022-11-09 23:08:39 UTC  
> Url: https://github.com/boostorg/url/issues/622  

The library, tests, and examples all emit warnings when building 32-bit targets under msvc.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-11-08 02:02:50 UTC  
> Url: https://github.com/boostorg/url/issues/622#issuecomment-1306509108  

To build the tests under 32-bit with `b2`, we need b2 address-model=32.
