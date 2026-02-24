# #132 - PFR doesn't work with `std::unique_ptr<T>` field since MSVC v19.25 [Closed]

> Username: denzor200  
> Created at: 2023-07-01 12:28:19 UTC  
> Updated at: 2025-06-20 14:52:01 UTC  
> Closed at: 2025-06-20 14:52:01 UTC  
> Url: https://github.com/boostorg/pfr/issues/132  

https://godbolt.org/z/16Go1bYv3  
In MSVC v19.24 it works

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-06-20 14:52:01 UTC  
> Url: https://github.com/boostorg/pfr/issues/132#issuecomment-2991923626  

Looks like it has been fixed in 19.29 or even earlier https://regression.boost.io/develop/developer/pfr.html
