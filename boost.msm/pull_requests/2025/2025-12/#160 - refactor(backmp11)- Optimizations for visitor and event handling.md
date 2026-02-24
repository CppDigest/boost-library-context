# #160 refactor(backmp11): Optimizations for visitor and event handling [Merged]

> Username: chandryan  
> Created at: 2025-12-19 11:53:38 UTC  
> Updated at: 2025-12-19 14:10:42 UTC  
> Merged at: 2025-12-19 14:10:38 UTC  
> Closed at: 2025-12-19 14:10:38 UTC  
> Url: https://github.com/boostorg/msm/pull/160  

Applied optimizations:  
  
- No-op visitor specialization if there are no states to visit  
- Improved handling of event enqueing and deferral by using a visitor with compile-time filtering and omitting `std::function`

---
