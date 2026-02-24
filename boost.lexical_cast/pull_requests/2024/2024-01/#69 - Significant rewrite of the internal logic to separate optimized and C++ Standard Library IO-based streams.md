# #69 Significant rewrite of the internal logic to separate optimized and C++ Standard Library IO-based streams [Merged]

> Username: apolukhin  
> Created at: 2024-01-21 15:31:37 UTC  
> Updated at: 2024-01-24 19:35:44 UTC  
> Merged at: 2024-01-24 19:35:41 UTC  
> Closed at: 2024-01-24 19:35:41 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/69  

* C++ Standard Library based streams now constructed in less cases leading to [*better performance];  
  * less template instantiations and simpler maintainance;  
  * support for `volatile` input types was dropped, following the C++ Standard Library trend.

---
