# #62 Simplify CMake [Closed]

> Username: Flamefire  
> Created at: 2020-01-21 16:35:29 UTC  
> Updated at: 2020-01-24 13:04:44 UTC  
> Closed at: 2020-01-23 13:30:58 UTC  
> Url: https://github.com/boostorg/nowide/pull/62  

Remove LAYOUT and SYSTEM_INCLUDE parameters (handled by superproject if wanted).  
Remove BoostAddLibrary and add the options in the "classic" way.  
Refactor BoostInstall into a generic InstallTargets function that wraps  
all boilerplate code required for almost all libraries.

---
