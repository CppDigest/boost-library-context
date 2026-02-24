# #398 Update nodiscard attribute: [Merged]

> Username: jzmaddock  
> Created at: 2021-08-19 17:18:19 UTC  
> Updated at: 2022-11-20 17:45:34 UTC  
> Merged at: 2021-08-19 18:22:01 UTC  
> Closed at: 2021-08-19 18:22:02 UTC  
> Url: https://github.com/boostorg/config/pull/398  

It's not supported on functions pre-c++11 even if __has_cpp_attribute indicates it is supported.  
Also update test case.  
Fixes https://github.com/boostorg/config/issues/336.

---
