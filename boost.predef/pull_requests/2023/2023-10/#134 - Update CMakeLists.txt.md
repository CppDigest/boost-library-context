# #134 Update CMakeLists.txt [Closed]

> Username: pdimov  
> Created at: 2023-10-28 22:53:09 UTC  
> Updated at: 2023-11-01 01:25:51 UTC  
> Closed at: 2023-11-01 01:25:51 UTC  
> Url: https://github.com/boostorg/predef/pull/134  

* Remove obsolete comment  
* Bump minimum version to 3.5 to avoid the annoying CMake warning  
```  
CMake Deprecation Warning at /Users/runner/work/system/boost-root/libs/predef/CMakeLists.txt:23 (cmake_minimum_required):  
  Compatibility with CMake < 3.5 will be removed from a future version of  
  CMake.  
  
  Update the VERSION argument <min> value or use a ...<max> suffix to tell  
  CMake that the project does not need compatibility with older versions.  
```  
* Use project name `boost_predef` as this is the Boost convention  
* Use `VERSION "${BOOST_SUPERPROJECT_VERSION}"` per Boost convention. This sets the project version to the Boost version if the library is part of the Boost superproject, and otherwise leaves it empty, as before  
* Update comment to reflect changes

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2023-11-01 01:25:51 UTC  
> Url: https://github.com/boostorg/predef/pull/134#issuecomment-1788257116  

Resolved with https://github.com/boostorg/predef/commit/614546d6fac1e68cd3511d3289736f31d5aed1eb

---
