# #17 Use uncaught_exceptions from Boost.Core to avoid C++17 warnings [Merged]

> Username: Lastique  
> Created at: 2018-12-17 15:56:50 UTC  
> Updated at: 2021-06-01 09:11:24 UTC  
> Merged at: 2021-06-01 08:23:55 UTC  
> Closed at: 2021-06-01 08:23:55 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/17  

In C++17, `std::uncaught_exception` is deprecated in favor of `std::uncaught_exceptions`. Use portable `uncaught_exceptions` implementation from Boost.Core, which is also available for C++03.  
  
This fixes gcc 8 warnings about using deprecated features in C++17 mode.

---
