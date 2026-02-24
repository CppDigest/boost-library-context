# #65 Do export(...) to Boost targets [Merged]

> Username: Pansysk75  
> Created at: 2024-06-17 18:23:16 UTC  
> Updated at: 2024-06-19 15:23:22 UTC  
> Merged at: 2024-06-19 15:23:22 UTC  
> Closed at: 2024-06-19 15:23:22 UTC  
> Url: https://github.com/boostorg/cmake/pull/65  

Hello,  
  
We have a library that has a dependency on Boost. We use CMake's [`export(...)`](https://cmake.org/cmake/help/latest/command/export.html) on our library's targets, so that the targets are exported on the build tree (in addition to exporting them on installation, which is done when using `install(EXPORT ...)`).  
This has worked when an installed Boost is used, since the Boost targets are introduced as IMPORTED targets, and thus CMake does not attempt to "export" them when we export our library.   
  
We are now trying to add an option for automatically fetching Boost using FetchContent, and thus installing Boost alongside our library. Now, since the Boost targets are not IMPORTED (but instead are "real", buildable targets), CMake will also try to "export" them when exporting our library, since our library is PUBLIC-ly linked to Boost targets.  
  
We get the following errors when configuring our library:  
```  
CMake Error in CMakeLists.txt:  
  export called with target "mylibrary_dependencies_boost" which requires target  
  "boost_phoenix" that is not in any export set.  
```  
  
Adding this single line in BoostInstall.cmake will export the Boost targets inside Boost's build directory on configuration time, and allows us to use `export(...)` on our library, as all transitive dependencies are now also `export(...)`ed.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-06-18 01:38:45 UTC  
> Url: https://github.com/boostorg/cmake/pull/65#issuecomment-2174745485  

I'm not entirely sure where's the proper place for the exported targets, as the `cmake/` directory seems somewhat arbitrary, but I don't have any better suggestions.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-06-18 04:10:17 UTC  
> Url: https://github.com/boostorg/cmake/pull/65#issuecomment-2174948844  

Maybe we should put them into `export/` or `exported/`?

---

## Comment 3

> Username: Pansysk75  
> Created_at: 2024-06-18 17:10:48 UTC  
> Url: https://github.com/boostorg/cmake/pull/65#issuecomment-2176589177  

Sure, I have no issue with that,   
  
Also, I'm not that experienced with CMake and I just happened to find something that works for me, feel free to push back on this if you have any doubts. It shouldn't affect anything, apart from generating a few additional files.  
  
Thanks for the quick response :)

---
