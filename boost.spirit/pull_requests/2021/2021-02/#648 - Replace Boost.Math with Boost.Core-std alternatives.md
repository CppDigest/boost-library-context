# #648 Replace Boost.Math with Boost.Core/std alternatives [Merged]

> Username: Kojoley  
> Created at: 2021-02-14 22:08:38 UTC  
> Updated at: 2021-02-15 12:21:14 UTC  
> Merged at: 2021-02-15 12:21:13 UTC  
> Closed at: 2021-02-15 12:21:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/648  

It might cost us backward compatibility with ancient compilers, exotic platforms, and yield a performance difference on MSVC, but Boost.Math is C++11 now, and Clang-cl can (or even should) be used instead of MSVC where performance matters.

---

## Comment 1

> Username: djowel  
> Created_at: 2021-02-14 23:28:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/648#issuecomment-778860325  

Sounds good!

---
