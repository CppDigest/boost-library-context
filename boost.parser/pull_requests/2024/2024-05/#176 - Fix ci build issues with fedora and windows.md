# #176 Fix ci build issues with fedora and windows [Closed]

> Username: d5ch4k  
> Created at: 2024-05-06 13:10:32 UTC  
> Updated at: 2024-10-01 19:07:21 UTC  
> Closed at: 2024-09-30 17:44:53 UTC  
> Url: https://github.com/boostorg/parser/pull/176  

this PR would fix the issues with the CI system (Fedora GCC-14), Windows MSVC 2022 c++23 and additionally a compiler error for test/search.cpp with certain clang compilers for c++23 (see also #168)  
All 3 issues have a separate git commit.  
However, I'm not 100% certain, if the fixes are appropriate for your intentions or coding style

---

## Comment 1

> Username: tzlaine  
> Created_at: 2024-09-30 17:44:53 UTC  
> Url: https://github.com/boostorg/parser/pull/176#issuecomment-2383814276  

Thanks for doing this.  I unfortunately didn't notice it until these things were fixed separately.  The MSVC 20222 fix probably is the wrong fix, since this is going to come up for the other compilers eventually as well.  The right fix IMO is #190.

---
