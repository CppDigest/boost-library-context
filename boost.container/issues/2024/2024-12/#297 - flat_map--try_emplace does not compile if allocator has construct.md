# #297 - flat_map::try_emplace does not compile if allocator has construct [Closed]

> Username: jens-diewald  
> Created at: 2024-12-12 08:56:57 UTC  
> Updated at: 2024-12-22 21:02:15 UTC  
> Closed at: 2024-12-22 21:02:15 UTC  
> Url: https://github.com/boostorg/container/issues/297  

Using `flat_map` with an Allocator that provides a `construct` member function and using `try_emplace` with that `flat_map` causes a compiler error.  
  
The problem occurs with Boost 1.86 independent of platform/compiler, see below for examples.  
  
`boost::container::allocator_traits<Allocator>::construct` does a case distinction on (simplified) `is_not_std_allocator<Allocator> && has_member_function_callable_with_construct<Allocator, Args...>`. If that evaluates to `true`, a code path is taken, that has no special treatment for `boost::container::try_emplace_t`. That causes the `Allocator::construct` member function to emplace something into the map with args roughly `try_emplace_t, KeyType, ValueTypeArgs...`.  
  
Maybe the case distinction can be moved further down into `dtl::construct_type`?  
What is the special treatment of `std::allocator` for, by the way?  
  
Minimal example with C++20 ([godbolt](https://godbolt.org/z/Y7Y6xb8T6)):  
```c++  
#include <memory_resource>  
#include <boost/container/flat_map.hpp>  
  
int main()  
{  
    using Allocator = std::pmr::polymorphic_allocator<std::pair<int, int>>;  
    boost::container::flat_map<int, int, std::less<int>, Allocator> map;  
    map.try_emplace(1, 1);  
}  
```  
Note that C++20 is necessary, since apparently `polymorphic_allocator<T>::construct` was explicitly disabled for `std::pair` before ([cppreference](https://en.cppreference.com/w/cpp/memory/polymorphic_allocator/construct)).  
  
We ran into this with `mi_stl_allocator` from [mimalloc](https://github.com/microsoft/mimalloc). That produces the error independent of C++ standard ([godbolt](https://godbolt.org/z/b1o6EvfMM)).  
  
As far as I can tell, one can (hackily) work around this by specializing `is_stl_allocator` or `has_member_function_callable_with_construct`, as long as the allocators construct function only does placement new anyway.
