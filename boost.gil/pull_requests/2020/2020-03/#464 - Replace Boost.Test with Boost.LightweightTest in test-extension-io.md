# #464 Replace Boost.Test with Boost.LightweightTest in test/extension/io [Merged]

> Username: mloskot  
> Created at: 2020-03-23 20:34:37 UTC  
> Updated at: 2020-03-26 19:37:55 UTC  
> Merged at: 2020-03-24 09:21:20 UTC  
> Closed at: 2020-03-24 09:21:21 UTC  
> Url: https://github.com/boostorg/gil/pull/464  

### Description  
  
Move test sources to format-specific directories.  
Add FIXME and TODO comments about issues discovered while building the format-specific tests.  
Add missing `#include`-s with standard library headers.  
Apply minor corrections and tidying up.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- Continues #459  
- Closes #461  
- This is a change of direction, reverts #417  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Move test sources to format-specific directories (e.g. test/extension/io/jpeg_*.cpp to test/extension/io/jpeg/*.cpp)  
- [x] Refactor each .cpp file as separate test with main using Boost.LT  
- [x] Update `Jamfile` files to catch up with the new folders layout  
- [x] Update `CMakeLists.txt`. For now, only for the `simple` target. The `full` is never built on CI and we are going to re-work CMake anyway (see #463)  
- [x] Ensure all CI builds pass

---
