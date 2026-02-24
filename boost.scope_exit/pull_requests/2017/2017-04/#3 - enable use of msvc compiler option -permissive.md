# #3 enable use of msvc compiler option /permissive- [Merged]

> Username: DanielaE  
> Created at: 2017-04-15 06:53:37 UTC  
> Updated at: 2017-12-21 06:42:44 UTC  
> Merged at: 2017-11-22 01:06:32 UTC  
> Closed at: 2017-11-22 01:06:32 UTC  
> Url: https://github.com/boostorg/scope_exit/pull/3  

With the updated Boost.Typeof library capable of handling /permissive, the typeof workaround for msvc in Boost.Scope_Exit is no longer required. Even worse, the workaround code causes compile errors.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-04-15 06:57:41 UTC  
> Url: https://github.com/boostorg/scope_exit/pull/3#issuecomment-294276630  

This applies to msvc 14.1 from Visual Studio 2017.  
  
The full Boost test-suite passes with identical results as without this patch applied. The patch assumes that the related typeof [pull-request #7](https://github.com/boostorg/typeof/pull/7) is merged.

---

## Review 2 [Commented]

> Username: MarcelRaad  
> Created_at: 2017-06-07 14:57:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/scope_exit/pull/3#pullrequestreview-42630284  

📁 include/boost/scope_exit.hpp

```diff
  63 | 
  64 |- #if BOOST_MSVC
  64 |+ #if BOOST_MSVC && (BOOST_MSVC <= 1900)
```

> Username: MarcelRaad  
> Created_at: 2017-06-07 14:57:30 UTC  
> Updated_at: 2017-06-07 14:57:31 UTC  
> Url: https://github.com/boostorg/scope_exit/pull/3#discussion_r120650278  

Maybe use MSVC_TYPEOF_HACK instead to make sure that future changes to config or typeof don't break anything?

> Username: OlafvdSpek  
> Created_at: 2017-11-17 08:23:31 UTC  
> Url: https://github.com/boostorg/scope_exit/pull/3#discussion_r151625664  

Wouldn't < 1901 be better (assuming this was updated in 1901)?


---
