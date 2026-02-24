# #593 - Export compile database [Open]

> Username: EugeneZelenko  
> Created at: 2020-05-06 13:10:58 UTC  
> Updated at: 2021-05-29 17:22:25 UTC  
> Url: https://github.com/boostorg/build/issues/593  

It'll be great to have command line option to export compile database, like CMake's [CMAKE_EXPORT_COMPILE_COMMANDS](https://cmake.org/cmake/help/v3.17/variable/CMAKE_EXPORT_COMPILE_COMMANDS.html).  
  
Currently it's necessary to use tools like [Bear](https://github.com/rizsotto/Bear) or [scan-build-py](https://github.com/llvm/llvm-project/tree/master/clang/tools/scan-build-py).  
  
This would allow to run additional tools like Clang-tidy, Include What You Use, etc. Eventually such tool may become part of Boost pre-commit builds.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-05-06 13:31:17 UTC  
> Url: https://github.com/boostorg/build/issues/593#issuecomment-624651097  

Yes, that would be good to have. And it's been a long standing goal to have that. It's not easy to achieve though because B2 allows arbitrary shell commands to be invoked in the toolsets. Best we can easily do now is to dump a combined shell file of everything that's run.

---

## Comment 2

> Username: arvidn  
> Created at: 2020-08-10 06:19:17 UTC  
> Url: https://github.com/boostorg/build/issues/593#issuecomment-671182132  

I made an attempt at this a while ago https://github.com/boostorg/build/pull/133

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:55 UTC  
> Url: https://github.com/boostorg/build/issues/593#issuecomment-850868237  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
