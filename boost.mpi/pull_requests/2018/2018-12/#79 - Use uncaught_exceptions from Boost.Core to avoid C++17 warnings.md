# #79 Use uncaught_exceptions from Boost.Core to avoid C++17 warnings [Merged]

> Username: Lastique  
> Created at: 2018-12-17 16:01:55 UTC  
> Updated at: 2019-04-14 16:31:34 UTC  
> Merged at: 2019-03-11 14:15:01 UTC  
> Closed at: 2019-03-11 14:15:02 UTC  
> Url: https://github.com/boostorg/mpi/pull/79  

In C++17, `std::uncaught_exception` is deprecated in favor of `std::uncaught_exceptions`. Use portable `uncaught_exceptions` implementation from Boost.Core, which is also available for C++03.  
  
This fixes gcc 8 warnings about using deprecated features in C++17 mode.

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-04-13 16:27:58 UTC  
> Url: https://github.com/boostorg/mpi/pull/79#issuecomment-482832481  

Looks like it didn't make it to 1.70. Can this be merged to master, please?

---

## Comment 2

> Username: aminiussi  
> Created_at: 2019-04-14 16:31:34 UTC  
> Url: https://github.com/boostorg/mpi/pull/79#issuecomment-483011879  

develop has been merged to master

---
