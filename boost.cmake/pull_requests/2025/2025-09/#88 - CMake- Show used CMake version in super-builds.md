# #88 CMake: Show used CMake version in super-builds [Merged]

> Username: Flamefire  
> Created at: 2025-09-11 13:45:29 UTC  
> Updated at: 2025-09-12 07:08:53 UTC  
> Merged at: 2025-09-11 23:46:05 UTC  
> Closed at: 2025-09-11 23:46:05 UTC  
> Url: https://github.com/boostorg/cmake/pull/88  

In super-builds show the CMake version which is especially useful on CI to verify the used CMake and/or compare different jobs on failure.  
  
Limited to the super build to not be too verbose when Boost is used via add_subdirectory or similar.  
I kept the original call in the latter situation using VERBOSE

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-09-11 13:48:28 UTC  
> Url: https://github.com/boostorg/cmake/pull/88#issuecomment-3280820605  

Let's not touch the unrelated prefix logic. Just check the condition again.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-09-11 14:10:58 UTC  
> Url: https://github.com/boostorg/cmake/pull/88#issuecomment-3280971687  

> Let's not touch the unrelated prefix logic. Just check the condition again.  
  
To me it's easier to see all logic happening for super-builds in one block, but sure, easy enough

---

## Comment 3

> Username: nigels-com  
> Created_at: 2025-09-11 21:02:48 UTC  
> Url: https://github.com/boostorg/cmake/pull/88#issuecomment-3282626513  

Looks good to me.

---
