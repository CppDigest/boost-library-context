# #212 Reimplement `string_set` as `any_string` [Merged]

> Username: Flamefire  
> Created at: 2024-01-23 18:12:16 UTC  
> Updated at: 2024-01-24 18:07:13 UTC  
> Merged at: 2024-01-24 18:07:10 UTC  
> Closed at: 2024-01-24 18:07:10 UTC  
> Url: https://github.com/boostorg/locale/pull/212  

Use a better name for a type-erased string implemented using `dynamic_cast` instead of storing&comparing the `typeid` of the char type used.  
This is a workaround for missing typeinfo for `char8_t` on e.g. libc++ on FreeBSD 13.1.  
It also simplifies memory management size calc/copy implementation.

---
