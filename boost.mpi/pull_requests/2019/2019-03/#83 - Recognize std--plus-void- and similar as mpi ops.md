# #83 Recognize std::plus<void> and similar as mpi ops [Closed]

> Username: fweik  
> Created at: 2019-03-27 20:13:44 UTC  
> Updated at: 2019-03-30 20:31:10 UTC  
> Closed at: 2019-03-30 20:31:10 UTC  
> Url: https://github.com/boostorg/mpi/pull/83  

C++14 introduces `std::plus<void>` and similar functors, that determine the type by argument type deduction on the operator (so that you don't have to specify the type). This adds overloads to recognize those as native mpi ops.

---
