# #33 Make sure std::string constructor is available [Merged]

> Username: muggenhor  
> Created at: 2015-12-16 13:54:46 UTC  
> Updated at: 2015-12-23 14:44:17 UTC  
> Merged at: 2015-12-23 09:33:24 UTC  
> Closed at: 2015-12-23 09:33:24 UTC  
> Url: https://github.com/boostorg/container/pull/33  

Make sure `std::string` constructor is available because `<stdexcept>` only needs to have a forward declaration of `std::string`. But that's not enough to invoke constructors of `std::exception` (and derivatives) if we don't have a reference to an `std::string` handy.  
  
This is necessary with STLport which only includes a forward declaration of `std::string` in `<stdexcept>`.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2015-12-23 09:33:38 UTC  
> Url: https://github.com/boostorg/container/pull/33#issuecomment-166844794  

Many thanks for the patch and the detailed report.

---
