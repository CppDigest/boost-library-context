# #187 Add libdwfl based implementation [Open]

> Username: kanje  
> Created at: 2024-09-19 21:55:21 UTC  
> Updated at: 2025-01-24 08:25:44 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/187  

This commit adds an implementation based on `libdwfl` from `elfutils`.  
  
Implements #176

---

## Comment 1

> Username: kanje  
> Created_at: 2024-09-19 22:03:09 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/187#issuecomment-2362279985  

@apolukhin, please have a look at this draft. Note, I was using CMake and `b2` support is still missing. I would appreciate some help here as I know nothing about how to write Jamfiles.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2024-10-16 12:19:43 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/187#issuecomment-2416668517  

Tests are missing. You will need to write some b2 scripts: follow this sample https://github.com/boostorg/stacktrace/blob/344f6bae1929c87fa62a601ea53433d2b79ee0c0/build/Jamfile.v2#L80-L93 and add code to search for libdwfl https://github.com/boostorg/stacktrace/blob/344f6bae1929c87fa62a601ea53433d2b79ee0c0/build/Jamfile.v2#L34-L40  
  
After that adjust https://github.com/boostorg/stacktrace/blob/develop/test/Jamfile.v2 by adding each of the tests with libdwfl

---

## Review 3 [Changes requested]

> Username: apolukhin  
> Created_at: 2025-01-24 08:25:43 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/stacktrace/pull/187#pullrequestreview-2572004402  

Sorry for the long delay. The PR looks very good!  
  
A few more things to fix:  
* As I see in CI runs the `dw` library is not found and the new functionality is not tested. Please adjust the content of `.github/workflows/`  directory to test the new implementation  
* One of the Boost specific linters complains that the build/has_dwfl.cpp, include/boost/stacktrace/detail/libdwfl_impls.hpp, src/dwfl.cpp files have no `Copyright` notes. Copypaste the Copyright message and place your name in it (or place my name if you wish to remain anonymous)

---
