# #498 Correct static initialization. [Merged]

> Username: jzmaddock  
> Created at: 2022-10-01 18:28:55 UTC  
> Updated at: 2022-10-03 16:18:18 UTC  
> Merged at: 2022-10-03 16:18:17 UTC  
> Closed at: 2022-10-03 16:18:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/498  

Previous changes to use C++11 thread safe statics mangled a few static variables leaving them non-thread-safe initialized. Do a quick review through the code to fix the remaining issues. Fixes https://github.com/boostorg/multiprecision/issues/497.

---
