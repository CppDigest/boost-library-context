# #45 Correct CMake version check; VERSION_GREATER 3.18 is true for 3.18.1 [Merged]

> Username: pdimov  
> Created at: 2025-10-19 18:15:07 UTC  
> Updated at: 2025-10-20 07:02:09 UTC  
> Merged at: 2025-10-20 07:01:56 UTC  
> Closed at: 2025-10-20 07:01:56 UTC  
> Url: https://github.com/boostorg/bloom/pull/45  

Also move block before `add_subdirectory(test)` and remove extraneous `**` implied by `GLOB_RECURSE`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-19 18:16:44 UTC  
> Url: https://github.com/boostorg/bloom/pull/45#issuecomment-3419856617  

Since the minimum CMake version is 3.8 here, `VERSION_GREATER_EQUAL 3.18` (requires CMake 3.7) is also possible, but the current formulation matches the checks in the rest of the libraries containing a similar block of code.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-10-19 18:22:28 UTC  
> Url: https://github.com/boostorg/bloom/pull/45#issuecomment-3419860085  

An automated preview of the documentation is available at [https://45.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://45.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-10-19 18:22:27 UTC

---
