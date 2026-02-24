# #185 Workaround clang bug with `basic_string_view` [Open]

> Username: Flamefire  
> Created at: 2025-01-04 11:08:24 UTC  
> Updated at: 2025-01-04 13:46:35 UTC  
> Url: https://github.com/boostorg/core/pull/185  

Clang < 15 has a bug in c++-20 mode where using the `std::basic_string` constructor taking a pointer and size leads to an undefined symbol `M_construct` when used with Unicode char types.  
  
See https://github.com/llvm/llvm-project/issues/55560  
  
Avoid this by using the range constructor.  
  
Note that when converting this string_view to a std string the bug is triggered again because in `libstdc++` the `basic_string` ctor from `std::basic_string_view` delegates to the size-range ctor and is instantiated if the source type is convertible to `std::basic_string_view`.      
This can be verified by commenting out the `std::basic_string_view` conversion operator in `boost::core::basic_string_view`   
  
Using libc++ also works.

---
