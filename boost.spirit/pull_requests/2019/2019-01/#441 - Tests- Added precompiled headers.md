# #441 Tests: Added precompiled headers [Merged]

> Username: Kojoley  
> Created at: 2019-01-20 17:43:24 UTC  
> Updated at: 2019-01-20 23:59:51 UTC  
> Merged at: 2019-01-20 23:59:46 UTC  
> Closed at: 2019-01-20 23:59:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/441  

Not intrusive. Significantly speeds up tests on MSVC (for about 3 times). Finally solves exceeding build time limits on CI with MSVC compilers.  
  
Did not set up PCH for X3, it is fast as-is (tooks only about 2 minutes). Repository tests reuse PCH from Qi/Karma tests.

---

## Comment 1

> Username: djowel  
> Created_at: 2019-01-20 22:39:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/441#issuecomment-455909341  

So cool!

---
