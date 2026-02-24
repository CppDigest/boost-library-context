# #3 Fix missing include enable_if.hpp [Merged]

> Username: rogiervd  
> Created at: 2014-07-10 15:12:06 UTC  
> Updated at: 2026-02-19 02:10:56 UTC  
> Merged at: 2014-07-10 16:29:44 UTC  
> Closed at: 2014-07-10 16:29:44 UTC  
> Url: https://github.com/boostorg/math/pull/3  

I think there is a missing #include in bernoulli_details.hpp for enable_if_c, which is used in line 179. The unit tests failed for me and this fixes it. (Apologies for a malformed pull request earlier; used to Darcs, git baffles me.)

---
