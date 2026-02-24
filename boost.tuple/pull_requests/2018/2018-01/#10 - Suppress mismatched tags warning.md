# #10 Suppress mismatched tags warning [Merged]

> Username: Kojoley  
> Created at: 2018-01-05 13:04:41 UTC  
> Updated at: 2018-01-05 22:34:43 UTC  
> Merged at: 2018-01-05 22:11:12 UTC  
> Closed at: 2018-01-05 22:11:12 UTC  
> Url: https://github.com/boostorg/tuple/pull/10  

MSVC defines them with a `struct` keyword so I think it is not necessary to suppress MSVC C4099 warning. But if you wish I will add it too.

---
