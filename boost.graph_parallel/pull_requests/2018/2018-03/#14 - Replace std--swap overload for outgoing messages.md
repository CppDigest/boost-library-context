# #14 Replace std::swap overload for outgoing messages [Merged]

> Username: jwakely  
> Created at: 2018-03-17 13:31:06 UTC  
> Updated at: 2021-06-01 08:45:32 UTC  
> Merged at: 2021-06-01 08:24:13 UTC  
> Closed at: 2021-06-01 08:24:13 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/14  

Adding overloads to namespace std is undefined behaviour. The correct  
way to customize swap is to provide an overload in the type's own  
namespace, so it will be found by ADL.

---
