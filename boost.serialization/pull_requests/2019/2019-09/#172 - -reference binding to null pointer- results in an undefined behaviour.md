# #172 "reference binding to null pointer" results in an undefined behaviour [Merged]

> Username: TaWeiTu  
> Created at: 2019-09-01 22:04:59 UTC  
> Updated at: 2019-09-29 14:53:24 UTC  
> Merged at: 2019-09-29 14:53:24 UTC  
> Closed at: 2019-09-29 14:53:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/172  

As shown in the following picture, the 181-th line in `singleton.hpp` triggers UndefinedBehaviourSanitizer.   
  
https://imgur.com/a/1998FjL

---
