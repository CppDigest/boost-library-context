# #416 Remove Boost.Assign usages. [Merged]

> Username: gogagum  
> Created at: 2025-02-05 21:51:15 UTC  
> Updated at: 2025-02-10 02:11:59 UTC  
> Merged at: 2025-02-10 02:11:59 UTC  
> Closed at: 2025-02-10 02:11:59 UTC  
> Url: https://github.com/boostorg/graph/pull/416  

Boost.Assign is not needed C++14 code. This PR removes this library usages. It is also suggested to use `std::vector` `std::initializer_list` constructor instead of series of `push_back` calls in test/two_graphs_common_spanning_trees_test.cpp.

---

## Review 1 [Approved]

> Username: jeremy-murphy  
> Created_at: 2025-02-06 09:19:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/graph/pull/416#pullrequestreview-2598096183  

Thanks for a tidy improvement to the code! Just waiting for the tests to run.

---
