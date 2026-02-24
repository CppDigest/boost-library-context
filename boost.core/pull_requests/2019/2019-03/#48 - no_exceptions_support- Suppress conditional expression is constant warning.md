# #48 no_exceptions_support: Suppress conditional expression is constant warning [Merged]

> Username: Kojoley  
> Created at: 2019-03-26 14:03:39 UTC  
> Updated at: 2019-04-20 05:36:16 UTC  
> Merged at: 2019-04-20 05:36:15 UTC  
> Closed at: 2019-04-20 05:36:15 UTC  
> Url: https://github.com/boostorg/core/pull/48  



---

## Comment 1

> Username: pdimov  
> Created_at: 2019-04-19 14:35:26 UTC  
> Url: https://github.com/boostorg/core/pull/48#issuecomment-484914953  

I don't get this warning with msvc-14.0, 14.1, 14.2; only with 12.0 and below. So I wonder if the uglification is worth it.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-04-19 15:26:45 UTC  
> Url: https://github.com/boostorg/core/pull/48#issuecomment-484930923  

I agree that it looks ugly, but it does not cost you anything, and I do not think you look at the macro definition that often.

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-04-19 16:54:11 UTC  
> Url: https://github.com/boostorg/core/pull/48#issuecomment-484955483  

Is `_MSC_VER` (instead of `BOOST_MSVC`) intentional? It includes Borland (no idea if it's still alive), Clang, Intel.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-04-19 17:04:53 UTC  
> Url: https://github.com/boostorg/core/pull/48#issuecomment-484958298  

> Is `_MSC_VER` (instead of `BOOST_MSVC`) intentional? It includes Borland (no idea if it's still alive), Clang, Intel.  
  
No special reason here. Probably did not spotted that `boost/config.hpp` is already included back than. You want me to replace it? The compilers that pretends with other usually implements or ignores those things.  
  
> Borland (no idea if it's still alive)  
  
AFAIK they ship old Clang with their own extensions now.

---
