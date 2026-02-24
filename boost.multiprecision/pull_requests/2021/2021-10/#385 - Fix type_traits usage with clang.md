# #385 Fix type_traits usage with clang. [Merged]

> Username: jzmaddock  
> Created at: 2021-10-19 18:21:46 UTC  
> Updated at: 2021-10-20 16:03:05 UTC  
> Merged at: 2021-10-20 16:03:00 UTC  
> Closed at: 2021-10-20 16:03:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/385  

Prevents recursive instantiation of is_constructible.  
Fixes https://github.com/boostorg/multiprecision/issues/383.

---
