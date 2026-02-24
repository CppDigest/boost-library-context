# #903 Fix repeated builds of targets. [Merged]

> Username: grafikrobot  
> Created at: 2024-05-12 00:04:39 UTC  
> Updated at: 2024-05-12 03:39:16 UTC  
> Merged at: 2024-05-12 01:53:50 UTC  
> Closed at: 2024-05-12 01:53:50 UTC  
> Url: https://github.com/boostorg/boost/pull/903  

Accidentally added a dependency on the generated boost header tree for all targets. The boost headers generation only works if it's an implicit-dependency. As otherwise dependents think they are never up to date.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-05-12 00:05:00 UTC  
> Url: https://github.com/boostorg/boost/pull/903#issuecomment-2106063729  

FYI @pdimov

---
