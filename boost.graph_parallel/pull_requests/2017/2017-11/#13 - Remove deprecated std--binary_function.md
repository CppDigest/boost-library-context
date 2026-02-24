# #13 Remove deprecated std::binary_function [Merged]

> Username: pavelkryukov  
> Created at: 2017-11-21 17:07:53 UTC  
> Updated at: 2021-06-01 10:09:41 UTC  
> Merged at: 2021-06-01 08:43:47 UTC  
> Closed at: 2021-06-01 08:43:47 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/13  

`std::binary_function` was removed in C++17 and is no longer available while compiling with MS Visual Studio 2017

---

## Comment 1

> Username: fearsomepirate  
> Created_at: 2019-10-11 17:03:20 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/13#issuecomment-541145238  

@pavelkryukov Have you tried using boost::functional::binary_function instead?   
  
https://www.boost.org/doc/libs/1_71_0/boost/functional.hpp

---

## Comment 2

> Username: pavelkryukov  
> Created_at: 2019-10-12 08:50:20 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/13#issuecomment-541303125  

I believe it should be deprecated as well since C++11 has type deduction and perfect forwarding.

---

## Comment 3

> Username: fearsomepirate  
> Created_at: 2019-10-29 14:31:16 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/13#issuecomment-547449165  

Right, but this is failing integration checks. The minimum viable fix is probably to just replace `std::binary_function` with `boost::functional::binary_function`, and then those checks would probably stop failing.

---

## Comment 4

> Username: pavelkryukov  
> Created_at: 2019-10-29 16:44:15 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/13#issuecomment-547518570  

> The minimum viable fix is probably to just replace std::binary_function with boost::functional::binary_function,  
  
This is not the way the issue has been fixed in other Boost libraries, for instance:   
  
- https://github.com/boostorg/gil/pull/191  
- https://github.com/boostorg/mpi/pull/51  
- https://github.com/boostorg/algorithm/pull/18  
  
> and then those checks would probably stop failing.  
  
Could you please point to the place where checks fail due to absence of `std::binary_function` inheritance?

---
