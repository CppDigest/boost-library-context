# #195 - Why is the return type different for empty and non-empty types in boost::pfr::names_as_array? [Closed]

> Username: sabudilovskiy  
> Created at: 2025-03-13 12:39:55 UTC  
> Updated at: 2025-05-12 16:53:04 UTC  
> Closed at: 2025-05-12 16:53:04 UTC  
> Url: https://github.com/boostorg/pfr/issues/195  

The current implementation has the following code:  
```  
template <class T, std::size_t... I>  
constexpr auto make_stdarray_from_tietuple(const T& t, std::index_sequence<I...>, int) noexcept {  
    return detail::make_stdarray(  
        boost::pfr::detail::sequence_tuple::get<I>(t)...  
    );  
}  
  
template <class T>  
constexpr auto make_stdarray_from_tietuple(const T&, std::index_sequence<>, long) noexcept {  
    return std::array<std::nullptr_t, 0>{};  
}  
```  
Today I added an empty structure to my code and found that my old functions on top of boost::pfr exploded with some strange error, my code is quite simple:  
  
```  
template <typename T>  
void foo() {  
  constexpr auto names = boost::pfr::names_as_array<T>();  
  for (std::string_view name : names) {  
   //  
  }  
}  
```  
  
What is the motivation for such implementation?

---

## Comment 1

> Username: sabudilovskiy  
> Created at: 2025-03-13 12:46:03 UTC  
> Updated at: 2025-03-13 12:50:53 UTC  
> Url: https://github.com/boostorg/pfr/issues/195#issuecomment-2721136417  

[build] /home/sabudilovskiy/CPM/tgbm/64054d11fae5a85d11888d8a514d29dcd49f70a4/include/tgbm/utils/pfr_extension.hpp:34:25: error: conversion function from 'const value_type' (aka 'const std::nullptr_t') to 'std::string_view' (aka 'basic_string_view<char>') invokes a deleted function  
[build]    34 |   for (std::string_view name : names) {  
  
Error because C++ 23 delete std::string_view constructor from nullptr_t:   
https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p2166r1.html
