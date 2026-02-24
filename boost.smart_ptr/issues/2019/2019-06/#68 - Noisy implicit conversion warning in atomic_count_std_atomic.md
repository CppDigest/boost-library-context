# #68 - Noisy implicit conversion warning in atomic_count_std_atomic [Closed]

> Username: CJBussey  
> Created at: 2019-06-17 22:43:44 UTC  
> Updated at: 2019-06-23 13:02:35 UTC  
> Closed at: 2019-06-23 13:02:35 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/68  

The constructor of `boost::detail::atomic_count` defined in [atomic_count_std_atomic.hpp](https://github.com/boostorg/smart_ptr/blob/develop/include/boost/smart_ptr/detail/atomic_count_std_atomic.hpp#L29) implicitly casts a `long` to an `std::int_least32_t`. This causes a warning with clang `-Wshorten-64-to-32`.  
  
The warning is particularly noisey now since `boost::filesystem` introduced the usage of the atomic counter in `boost::filesystem::filesystem_error`:  
  
https://github.com/boostorg/filesystem/commit/498a090b531833355ea27cea94d0440c432f8afc  
  
The warning can be reproduced by including `boost/filesystem/operations.hpp` and compiling with clang with flags `-Wshorten-64-to-32` and `std=c++11` (or above).

---

## Comment 1

> Username: CJBussey  
> Created at: 2019-06-18 09:53:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/68#issuecomment-503034481  

876d40a
