# #148 Fix various warnings [Merged]

> Username: jefftrull  
> Created at: 2022-02-05 07:57:34 UTC  
> Updated at: 2022-02-05 23:44:20 UTC  
> Merged at: 2022-02-05 23:44:11 UTC  
> Closed at: 2022-02-05 23:44:11 UTC  
> Url: https://github.com/boostorg/wave/pull/148  

Address the following issues:  
  
1. comma operator inside square brackets (used in Phoenix expressions) is deprecated as of C++20  
2. different typed enums need some extra help to be "interoperable" (for arithmetic)  
3. Replace `tmpnam` calls with safer Boost.Filesystem features  
  
These are flagged by Clang (1 and 2) or the linker (3).  
  
Merging this PR will resolve #135 and #147

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2022-02-05 11:59:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/148#pullrequestreview-873927728  

LGTM, thanks!

---
