# #50 - Adapted iterator does not fulfill C++20 concept [Closed]

> Username: Tradias  
> Created at: 2021-03-13 12:48:38 UTC  
> Updated at: 2021-06-05 23:41:39 UTC  
> Closed at: 2021-06-05 23:41:39 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/50  

Adapting an existing iterator, e.g. `int*` using `friend boost::stl_interfaces::access` results in an iterator that does not fulfill the C++20 concepts, e.g. `std::random_access_iterator`. In particular `operator++` or `operator+=` does not return `Iterator&` as required by the concepts.  
  
Minimal example: https://godbolt.org/z/n6GbW8  
  
```  
#include <boost/stl_interfaces/iterator_interface.hpp>  
  
struct MyIterator : boost::stl_interfaces::iterator_interface<  
                        MyIterator, std::random_access_iterator_tag, int> {  
  MyIterator() = default;  
  
  // adding this makes it compile successfully  
  // constexpr auto& operator+=(difference_type count) noexcept {  
  //   ptr += count;  
  //   return *this;  
  // }  
  
  friend boost::stl_interfaces::access;  
  
  constexpr auto& base_reference() noexcept { return ptr; }  
  
  constexpr auto base_reference() const noexcept { return ptr; }  
  
  int* ptr{};  
};  
  
static_assert(std::random_access_iterator<MyIterator>);  
```  
  
Boost version: 1.75.0  
Affected compilers: All major ones that support concepts, see godbolt link
