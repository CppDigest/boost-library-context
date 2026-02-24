# #371 Adjust bootsrap for current B2 builds. [Merged]

> Username: grafikrobot  
> Created at: 2020-02-14 04:19:19 UTC  
> Updated at: 2020-03-05 12:39:32 UTC  
> Merged at: 2020-03-05 12:39:32 UTC  
> Closed at: 2020-03-05 12:39:32 UTC  
> Url: https://github.com/boostorg/boost/pull/371  

These are some minor adjustments to the bootstrap step of building B2. For bootstrap.bat/sh we no longer shove B2 build output to the bootstrap.log file as we want to see the helpful diagnostic messages from B2. For example, indicating the requirement of C++11 for the engine build. This also renames from Boost.Build to B2. And for bootstrap.bat adds clang as an option for bootstrap config creation.

---
