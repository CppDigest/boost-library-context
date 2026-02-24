# #156 TSAN annotations [Merged]

> Username: KochetovNicolai  
> Created at: 2020-12-17 14:50:36 UTC  
> Updated at: 2020-12-22 17:21:25 UTC  
> Merged at: 2020-12-22 07:12:44 UTC  
> Closed at: 2020-12-22 07:12:44 UTC  
> Url: https://github.com/boostorg/context/pull/156  

Annotate `ucontext` implementation using TSAN fiber switching API.  
  
Reference: this change is needed for ClickHouse CI to work with boost coroutines: https://github.com/ClickHouse/ClickHouse/pull/17868

---

## Comment 1

> Username: KochetovNicolai  
> Created_at: 2020-12-17 14:53:20 UTC  
> Url: https://github.com/boostorg/context/pull/156#issuecomment-747485698  

I have used `BOOST_USE_TSAN` macro for now. Don't know what is the proper way to do it.

---

## Comment 2

> Username: olk  
> Created_at: 2020-12-22 07:12:49 UTC  
> Url: https://github.com/boostorg/context/pull/156#issuecomment-749386995  

ty

---
