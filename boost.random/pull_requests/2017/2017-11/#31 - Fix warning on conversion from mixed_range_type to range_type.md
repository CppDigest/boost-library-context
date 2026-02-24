# #31 Fix warning on conversion from mixed_range_type to range_type [Closed]

> Username: Flamefire  
> Created at: 2017-11-03 15:48:08 UTC  
> Updated at: 2024-12-09 10:29:05 UTC  
> Closed at: 2024-12-09 10:29:04 UTC  
> Url: https://github.com/boostorg/random/pull/31  

This fixes a warning e.g. in VS:  
  
> warning C4244: "Argument": Conversion from "mixed_range_type" to "range_type", possible loss of data  
  
This was introduced with the mixed_range_type in https://github.com/boostorg/random/commit/d4514f1e075f43e5ea7b754529740442d091b6fe#diff-1c58e0310d5e97b63a4ec305eb8f7eb8L254

---
