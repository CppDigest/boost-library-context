# #103 Update CMakeLists.txt (fixes #102) [Merged]

> Username: pdimov  
> Created at: 2025-10-27 12:44:20 UTC  
> Updated at: 2025-10-28 00:34:07 UTC  
> Merged at: 2025-10-28 00:34:07 UTC  
> Closed at: 2025-10-28 00:34:07 UTC  
> Url: https://github.com/boostorg/sort/pull/103  

* Adds a GHA job for running the tests "standalone" (against a CMake-installed Boost)  
* Regenerates CMakeLists.txt using `boostdep --cmake sort` and then reapllies the standalone changes, but only when the library is the root project  
* Replaces setting the CMAKE_CXX_STANDARD unconditionally to 11 (which may override a root project or user choice of e.g. 17) with `target_compile_features`

---
