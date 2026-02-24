# #48 - Some CMake options are not marked as advanced [Closed]

> Username: DenizThatMenace  
> Created at: 2021-01-11 15:32:21 UTC  
> Updated at: 2022-07-11 11:06:24 UTC  
> Closed at: 2022-07-11 11:06:24 UTC  
> Url: https://github.com/boostorg/boost_install/issues/48  

When building a CMake project which depends on Boost and one uses `find_package(Boost CONFIG)` to find the installed Boost libraries, the CMake cache variables for Boost which are listed in `cmake-gui` are mostly marked as *advanced* (using the `mark_as_advanced` CMake command), however, not all of them are.  
  
These CMake cache variables have the form `boost_<library-name>_DIR` and point to the directories which contains the CMake config scripts for importing the associated Boost library.    
However, these variables exist for all such installed config scripts, even if the associated library is not provided for the build platform (and therefore not installed).  
Examples for such variables on a Linux platform are:  
* `boost_fiber_numa_DIR`  
* `boost_numpy_DIR`  
* `boost_stacktrace_windbg_DIR`  
* `boost_stracktrace_windbg_cached_DIR`  
  
In that case these variables are not marked as advanced and are therefore always shown, even if `cmake-gui` shall only display non-advanced options.  
  
**I recommend, these variables should still be marked as advanced.**

---

## Comment 1

> Username: pdimov  
> Created at: 2021-01-11 17:11:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/48#issuecomment-758093887  

Should be fixed with https://github.com/boostorg/boost_install/commit/c1878b042c43188184a49ac1e0ea9ddd84d1460e.
