# #32 Remove definition of BOOST_FIBERS_USE_VARIADIC_FIBER [Merged]

> Username: BenjaminW3  
> Created at: 2015-04-30 07:02:59 UTC  
> Updated at: 2016-06-06 11:36:34 UTC  
> Merged at: 2015-04-30 07:55:54 UTC  
> Closed at: 2015-04-30 07:55:54 UTC  
> Url: https://github.com/boostorg/fiber/pull/32  

Since the usage of variadic templates is now enabled by default and not emulated, the definition of the `BOOST_FIBERS_USE_VARIADIC_FIBER` macro can be removed.

---

## Comment 1

> Username: olk  
> Created_at: 2015-04-30 07:55:48 UTC  
> Url: https://github.com/boostorg/fiber/pull/32#issuecomment-97697148  

Thx - config.hpp needs this clean-up, sure

---
