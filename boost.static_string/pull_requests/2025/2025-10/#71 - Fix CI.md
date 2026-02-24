# #71 Fix CI [Merged]

> Username: Flamefire  
> Created at: 2025-10-06 12:36:46 UTC  
> Updated at: 2025-10-07 08:54:11 UTC  
> Merged at: 2025-10-06 15:22:47 UTC  
> Closed at: 2025-10-06 15:22:47 UTC  
> Url: https://github.com/boostorg/static_string/pull/71  

- Pass char arrays instead of `static_string` instances to `testR`, fixes https://github.com/boostorg/static_string/issues/70  
- Add missing Cxx11 B2 requirement, fixes GCC 4.x builds  
- Remove coverity & coverage jobs added in #64

---
