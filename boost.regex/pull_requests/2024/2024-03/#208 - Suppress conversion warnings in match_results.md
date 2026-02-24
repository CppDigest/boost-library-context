# #208 Suppress conversion warnings in match_results. [Merged]

> Username: jzmaddock  
> Created at: 2024-03-25 17:31:51 UTC  
> Updated at: 2024-03-25 17:42:43 UTC  
> Merged at: 2024-03-25 17:42:43 UTC  
> Closed at: 2024-03-25 17:42:43 UTC  
> Url: https://github.com/boostorg/regex/pull/208  

When passing std::size_t arguments to length(), str() and operator[] by making these templates enable_if'd on is_integral. Fixes https://github.com/boostorg/regex/issues/197.

---
