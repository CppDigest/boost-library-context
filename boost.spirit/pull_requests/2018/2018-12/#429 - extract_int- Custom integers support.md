# #429 extract_int: Custom integers support [Merged]

> Username: Kojoley  
> Created at: 2018-12-26 00:43:36 UTC  
> Updated at: 2019-01-13 12:20:56 UTC  
> Merged at: 2019-01-13 12:20:54 UTC  
> Closed at: 2019-01-13 12:20:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/429  

It will allow to test `extract_int` properly.  
  
Changed default `check_overflow` trait implementation to be `is_bounded`  
as it also covers float types and user data types.  
  
Replacing `boost::integer_traits` with `std::numeric_limits` will not hurt  
performance because even MSVC 9/GCC 4.1/Clang 3.0 folds `min`/`max` calls  
to a constant at compile time and since C++11 they are even constexpr.

---
