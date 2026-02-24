# #190 - Minor extra-semicolon warning issued for clang builds [Closed]

> Username: hadrielk  
> Created at: 2023-05-30 20:00:07 UTC  
> Updated at: 2023-06-21 22:35:07 UTC  
> Closed at: 2023-06-21 22:35:07 UTC  
> Url: https://github.com/boostorg/unordered/issues/190  

This is a minor nit, but the file `unordered/detail/foa.hpp` has a couple functions with extra semi-colons that trigger `clang`'s `extra-semi` warning:  
  
1. `maybe_caused_overflow()` on line `233`.  
2. `~destroy_on_exit()` on line `1719`.

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-05-31 03:44:03 UTC  
> Url: https://github.com/boostorg/unordered/issues/190#issuecomment-1569450975  

It's easy to add this check to our CI.  
  
For clarity, you're using this compiler option, correct?  
```  
-Wextra-semi-stmt  
```
