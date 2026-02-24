# #119 backmp11: Remove unnecessary headers [Merged]

> Username: chandryan  
> Created at: 2025-09-28 17:01:59 UTC  
> Updated at: 2025-09-28 19:40:47 UTC  
> Merged at: 2025-09-28 19:40:44 UTC  
> Closed at: 2025-09-28 19:40:44 UTC  
> Url: https://github.com/boostorg/msm/pull/119  

Remove not needed headers from backmp11.  
  
Header inclusions of boost::bind and boost::function are completely removed when building with backmp11  
The headers of boost::fusion are not directly included anymore in backmp11, though they are still present due to transitive dependencies.  
  
Related to https://github.com/boostorg/msm/issues/106

---
