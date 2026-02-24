# #63 Simplify cmake [Merged]

> Username: Flamefire  
> Created at: 2020-01-24 13:05:20 UTC  
> Updated at: 2020-01-28 13:43:19 UTC  
> Merged at: 2020-01-28 13:43:15 UTC  
> Closed at: 2020-01-28 13:43:15 UTC  
> Url: https://github.com/boostorg/nowide/pull/63  

Remove LAYOUT and SYSTEM_INCLUDE parameters (handled by superproject if wanted).  
Remove BoostAddLibrary and add the options in the "classic" way.  
Refactor BoostInstall into a generic InstallTargets function that wraps  
all boilerplate code required for almost all libraries.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2020-01-24 13:51:18 UTC  
> Url: https://github.com/boostorg/nowide/pull/63#issuecomment-578137457  

@pdimov Got everything working again. Did you test this already with your builds?

---
