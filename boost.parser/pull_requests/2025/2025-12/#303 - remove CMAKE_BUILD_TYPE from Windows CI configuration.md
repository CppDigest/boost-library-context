# #303 remove CMAKE_BUILD_TYPE from Windows CI configuration [Merged]

> Username: andreasbuhr  
> Created at: 2025-12-13 07:35:59 UTC  
> Updated at: 2026-01-25 22:06:22 UTC  
> Merged at: 2026-01-25 22:06:22 UTC  
> Closed at: 2026-01-25 22:06:22 UTC  
> Url: https://github.com/boostorg/parser/pull/303  

For Visual Studio, cmake generates just one vcxproj for all configurations. The presence of CMAKE_BUILD_TYPE on the command line leads to a warning.

---
