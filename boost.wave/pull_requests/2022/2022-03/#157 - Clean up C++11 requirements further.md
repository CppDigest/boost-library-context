# #157 Clean up C++11 requirements further [Merged]

> Username: jefftrull  
> Created at: 2022-03-09 20:33:30 UTC  
> Updated at: 2022-03-09 22:49:55 UTC  
> Merged at: 2022-03-09 22:49:54 UTC  
> Closed at: 2022-03-09 22:49:55 UTC  
> Url: https://github.com/boostorg/wave/pull/157  

- Test the requirement of "constexpr" for the deprecation error  
- Ensure C++11 requirements are checked for the samples build  
  
As @Flamefire noticed, subprojects like "test" and "samples" also need C++11 requirements for their builds to pass (or more accurately, not happen) with C++03 compilers. This adds those requirements for the samples.  
  
This also adds an explicit check on the availability of `constexpr` to the existing deprecation error, so the problem will be more obvious when someone attempts a manual build. It turns out that msvc-12 supports many parts of C++11 but not that one. Going forward we will need to update the check as we use additional C++11 features.

---
