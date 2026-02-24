# #176 - set::iterator should be constant [Open]

> Username: alexkaratarakis  
> Created at: 2021-01-13 23:33:22 UTC  
> Updated at: 2021-01-13 23:33:34 UTC  
> Url: https://github.com/boostorg/container/issues/176  

[`boost::container::set`'s `iterator`](https://github.com/boostorg/container/blame/develop/include/boost/container/set.hpp#L99) is a mutable iterator. This allows modification of keys, which can destroy the container's invariants, for example when using the erase-remove idiom.  
This used to be a [defect](https://cplusplus.github.io/LWG/issue103) in `std::set` as well but has been fixed.  
As can be seen in [cppreference](https://en.cppreference.com/w/cpp/container/set)  
  
```  
iterator | Constant LegacyBidirectionalIterator  
const_iterator | Constant LegacyBidirectionalIterator  
```  
  
and in stdlib:  
```C++  
      // _GLIBCXX_RESOLVE_LIB_DEFECTS  
      // DR 103. set::iterator is required to be modifiable,  
      // but this allows modification of keys.  
      typedef typename _Rep_type::const_iterator	 iterator;  
      typedef typename _Rep_type::const_iterator	 const_iterator;  
```
