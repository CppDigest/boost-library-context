# #467 Fix up usage of `std::move_backward` [Merged]

> Username: js-nano  
> Created at: 2025-03-06 08:11:09 UTC  
> Updated at: 2025-03-06 09:39:29 UTC  
> Merged at: 2025-03-06 09:39:28 UTC  
> Closed at: 2025-03-06 09:39:28 UTC  
> Url: https://github.com/boostorg/process/pull/467  

The destinations should point to the *end* of the destination range  
  
https://en.cppreference.com/w/cpp/algorithm/move_backward

---
