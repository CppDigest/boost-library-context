# #250 Add the BOOST_CONTEXT_USE_MAP_STACK logic to test_fcontext.cpp [Merged]

> Username: cjeker  
> Created at: 2024-02-13 10:00:17 UTC  
> Updated at: 2024-03-10 19:23:13 UTC  
> Merged at: 2024-03-10 19:23:04 UTC  
> Closed at: 2024-03-10 19:23:04 UTC  
> Url: https://github.com/boostorg/context/pull/250  

Without this test_fcontext.cpp fails on OpenBSD with a not-on-stack OS error.  
  
This is just bringing the same logic from the lib code to this test code.

---

## Comment 1

> Username: olk  
> Created_at: 2024-03-10 19:23:12 UTC  
> Url: https://github.com/boostorg/context/pull/250#issuecomment-1987337550  

ty

---
