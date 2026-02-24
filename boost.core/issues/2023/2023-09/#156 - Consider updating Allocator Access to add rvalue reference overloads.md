# #156 - Consider updating Allocator Access to add rvalue reference overloads [Open]

> Username: cmazakas  
> Created at: 2023-09-12 19:43:51 UTC  
> Updated at: 2023-09-12 19:43:51 UTC  
> Url: https://github.com/boostorg/core/issues/156  

Right now most of allocator access takes the allocator by lvalue reference.  
  
I presume this is to support stateful allocators.  
  
Instead, I purport that allocator access should also work with temporarily materialized allocators.  
  
This is useful in the case where we're rebinding a stored allocator and wishing to do a quick one-off allocation in a single place.  
  
The goal is to remove the need to declare a stack-local, for example.  
  
See the relevant CE example here: https://godbolt.org/z/hqKTGb3ab  
  
Obligatory inline copy:  
```cpp  
#include <boost/core/allocator_access.hpp>  
#include <memory>  
#include <type_traits>  
  
namespace boost  
{  
  
template<class A>  
allocator_pointer_t<std::decay_t<A>>  
allocator_allocate(A&& a, allocator_size_type_t<std::decay_t<A>> n)  
{  
  return allocator_allocate(a,n);  
}  
  
}  
  
auto tester(std::allocator<int> a) {  
  // feed the rebound allocator directly using the converting constructor  
  //  
  return boost::allocator_allocate(std::allocator<float>(a),16);  
  
  // want to avoid the need to declare `oa` to use the rebound allocator type  
  //  
  // std::allocator<float> oa(a);  
  // return boost::allocator_allocate(oa,16);  
}  
  
```
