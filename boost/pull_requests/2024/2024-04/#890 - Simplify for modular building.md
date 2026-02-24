# #890 Simplify for modular building. [Closed]

> Username: grafikrobot  
> Created at: 2024-04-24 18:46:31 UTC  
> Updated at: 2025-06-08 18:29:40 UTC  
> Closed at: 2025-06-08 18:29:40 UTC  
> Url: https://github.com/boostorg/boost/pull/890  

Modular building delegates, and normalizes, the building of tools inside the tools themselves. This makes it possible to avoid a bunch of code at this level. This change also adds default building for all current tools.  
  
NOTE: This depends on all the tools adjusting to the modular building first. Hence this is marked as WIP until then.

---
