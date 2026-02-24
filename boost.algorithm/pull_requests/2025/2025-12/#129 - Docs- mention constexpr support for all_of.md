# #129 Docs: mention constexpr support for all_of [Open]

> Username: Nadeem014-r  
> Created at: 2025-12-30 14:13:52 UTC  
> Updated at: 2025-12-30 17:01:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/129  

This PR updates the documentation of boost::algorithm::all_of to explicitly mention  
that the algorithm is constexpr when compiled with C++14 or later, provided the compiler  
supports constexpr evaluation.  
  
This addresses issue #83.

---

## Comment 1

> Username: mclow  
> Created_at: 2025-12-30 15:53:05 UTC  
> Url: https://github.com/boostorg/algorithm/pull/129#issuecomment-3699774499  

Nadeem - are there other algorithms where this text needs to be added?

---

## Comment 2

> Username: Nadeem014-r  
> Created_at: 2025-12-30 17:01:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/129#issuecomment-3699956031  

> Nadeem - are there other algorithms where this text needs to be added?  
  
Yes, there are likely other algorithms where this clarification would be helpful. I started with all_of as a minimal change. I can extend this to other algorithms as well if you’d like.

---
