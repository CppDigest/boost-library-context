# #32 - Boost.Contract no longer builds with C++03 [Closed]

> Username: mclow  
> Created at: 2023-11-10 17:32:33 UTC  
> Updated at: 2025-06-01 23:27:20 UTC  
> Closed at: 2025-06-01 23:27:20 UTC  
> Url: https://github.com/boostorg/contract/issues/32  

it includes `any.h`, which no longer supports C++03.  
  
Discovered in Boost 1.84.0 Beta 1 Release Candidate 1

---

## Comment 1

> Username: jeking3  
> Created at: 2025-01-10 20:40:01 UTC  
> Url: https://github.com/boostorg/contract/issues/32#issuecomment-2584079475  

There are also tests that explicitly set `BOOST_NO_CXX11_VARIADIC_TEMPLATES` to test C++03 compatibility instead of just forcing that test to run if cxxstd=03.  I found that both chrono and optional now fail under C++03.  I will probably just be removing the C++03 stuff; if someone tries to use it, one of the dependencies will tell them it's not supported.
