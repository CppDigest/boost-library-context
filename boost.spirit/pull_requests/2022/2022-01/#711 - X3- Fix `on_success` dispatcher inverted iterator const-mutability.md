# #711 X3: Fix `on_success` dispatcher inverted iterator const/mutability [Merged]

> Username: Kojoley  
> Created at: 2022-01-13 23:20:46 UTC  
> Updated at: 2022-01-15 17:28:50 UTC  
> Merged at: 2022-01-15 17:28:48 UTC  
> Closed at: 2022-01-15 17:28:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/711  

Instead of usual `[current, end)` pair it receives `[before, current)` pair.  
  
Fixes https://github.com/boostorg/spirit/issues/703#issuecomment-1011021773

---
