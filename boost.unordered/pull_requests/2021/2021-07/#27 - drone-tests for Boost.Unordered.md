# #27 drone-tests for Boost.Unordered [Closed]

> Username: tobias-loew  
> Created at: 2021-07-06 08:19:55 UTC  
> Updated at: 2022-06-10 21:16:42 UTC  
> Closed at: 2022-06-10 21:16:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/27  

With this PR there will be tests   
- running via github actions: mostly gcc and clang on ubuntu and some on windows  
- running via appveyor: Visual Studio, mostly msvc on windows but also some gcc on mingw/cygwin  
- running via drone: gcc & clang on ubuntu  
- removed tests via travis  
  
some tests via github actions and drone are failing  
- there seem to be version problems with clang and libc++ and libstdc++ (I'm not a clang expert, so I think it's up to somebody else to either fix the error or disable/remove the failing tests)  
  
Kudos to @sdarwin and @Flamefire for their support and work on boost-ci

---
