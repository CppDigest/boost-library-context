# #117 Fix compilation on Clang < 3.8 [Merged]

> Username: Flamefire  
> Created at: 2022-06-03 09:15:00 UTC  
> Updated at: 2022-06-03 16:32:17 UTC  
> Merged at: 2022-06-03 13:49:43 UTC  
> Closed at: 2022-06-03 13:49:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/117  

`val_alloc` is used in the other branch of the #ifdef too. CI Test runs for such old clangs are missing hence this went undetected. Added those in #114

---
