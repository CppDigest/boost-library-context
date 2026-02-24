# #56 Fix potentially untied streams [Merged]

> Username: Flamefire  
> Created at: 2020-01-05 15:25:54 UTC  
> Updated at: 2020-01-07 18:38:33 UTC  
> Merged at: 2020-01-05 15:45:17 UTC  
> Closed at: 2020-01-05 15:45:17 UTC  
> Url: https://github.com/boostorg/nowide/pull/56  

When using LTCG/LTO the unused initializer might be optimized out.  
Move the operation to the ctor

---
