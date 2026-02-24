# #103 Remove dependencies on Boost.StaticAssert [Merged]

> Username: Lastique  
> Created at: 2026-01-22 20:26:54 UTC  
> Updated at: 2026-01-23 16:10:05 UTC  
> Merged at: 2026-01-23 15:44:39 UTC  
> Closed at: 2026-01-23 15:44:39 UTC  
> Url: https://github.com/boostorg/cmake/pull/103  

Boost.StaticAssert has been merged into Boost.Config and its submodule should not be used anymore, so remove the dependency and tesing the library in CI.

---

## Comment 1

> Username: pdimov  
> Created_at: 2026-01-22 20:30:50 UTC  
> Url: https://github.com/boostorg/cmake/pull/103#issuecomment-3786560222  

The change to include_library.yml is wrong.

---

## Comment 2

> Username: Lastique  
> Created_at: 2026-01-22 20:59:41 UTC  
> Url: https://github.com/boostorg/cmake/pull/103#issuecomment-3786661084  

Why? The intent is to remove usage of the Boost.StaticAssert submodule, and the change removes its usage from CMake testing. Am I missing something?

---

## Comment 3

> Username: pdimov  
> Created_at: 2026-01-23 13:19:09 UTC  
> Url: https://github.com/boostorg/cmake/pull/103#issuecomment-3790201507  

I prefer to keep it in the list for now. If/when we remove it, I'll remove it.

---

## Comment 4

> Username: Lastique  
> Created_at: 2026-01-23 13:31:03 UTC  
> Url: https://github.com/boostorg/cmake/pull/103#issuecomment-3790250523  

Ok, as you wish, I removed the change to include_library.yml.

---

## Comment 5

> Username: pdimov  
> Created_at: 2026-01-23 15:44:30 UTC  
> Url: https://github.com/boostorg/cmake/pull/103#issuecomment-3790884873  

Thanks.

---
