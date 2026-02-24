# #5 aligned_allocator: cast away const-ness in construct [Closed]

> Username: timblechmann  
> Created at: 2016-05-14 18:43:32 UTC  
> Updated at: 2016-08-12 23:22:13 UTC  
> Closed at: 2016-08-12 23:21:28 UTC  
> Url: https://github.com/boostorg/align/pull/5  

clang compile fix: adding entries into a std::map via operator[] calls  
construct() on a const pointer.  
  
---  
  
```  
./external_libraries/boost/boost/align/aligned_allocator.hpp:99:33: error: static_cast from 'const nova::symbol *' to 'void *' is not allowed  
        ::new(const_cast<void*>(static_cast<void*>(ptr))) U(std::forward<Args>(args)...);  
                                ^~~~~~~~~~~~~~~~~~~~~~~  
/Applications/Xcode-7.3.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/memory:1647:18: note: in instantiation of function template specialization 'boost::alignment::aligned_allocator<std::__1::__tree_node<std::__1::__value_type<nova::symbol, int>, void *>, 64>::construct<const nova::symbol, const nova::symbol &>' requested here  
            {__a.construct(__p, _VSTD::forward<_Args>(__args)...);}  
                 ^  
/Applications/Xcode-7.3.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/memory:1493:14: note: in instantiation of function template specialization 'std::__1::allocator_traits<boost::alignment::aligned_allocator<std::__1::__tree_node<std::__1::__value_type<nova::symbol, int>, void *>, 64> >::__construct<const nova::symbol, const nova::symbol &>' requested here  
            {__construct(__has_construct<allocator_type, _Tp*, _Args...>(),  
             ^  
/Applications/Xcode-7.3.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/map:1522:20: note: in instantiation of function template specialization 'std::__1::allocator_traits<boost::alignment::aligned_allocator<std::__1::__tree_node<std::__1::__value_type<nova::symbol, int>, void *>, 64> >::construct<const nova::symbol, const nova::symbol &>' requested here  
    __node_traits::construct(__na, _VSTD::addressof(__h->__value_.__cc.first), __k);  
                   ^  
/Applications/Xcode-7.3.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/map:1538:29: note: in instantiation of member function 'std::__1::map<nova::symbol, int, std::__1::less<nova::symbol>, boost::alignment::aligned_allocator<std::__1::pair<nova::symbol, int>, 64> >::__construct_node_with_key' requested here  
        __node_holder __h = __construct_node_with_key(__k);  
                            ^  
../server/supernova/sc/sc_synthdef.cpp:235:22: note: in instantiation of member function 'std::__1::map<nova::symbol, int, std::__1::less<nova::symbol>, boost::alignment::aligned_allocator<std::__1::pair<nova::symbol, int>, 64> >::operator[]' requested here  
        parameter_map[data] = index;  
  
```

---

## Comment 1

> Username: timblechmann  
> Created_at: 2016-08-12 15:28:32 UTC  
> Url: https://github.com/boostorg/align/pull/5#issuecomment-239477813  

ping

---

## Comment 2

> Username: glenfe  
> Created_at: 2016-08-12 23:02:03 UTC  
> Url: https://github.com/boostorg/align/pull/5#issuecomment-239580076  

Sorry for the delay; I'll take a look this weekend and either merge this or otherwise fix it.

---

## Comment 3

> Username: glenfe  
> Created_at: 2016-08-12 23:21:27 UTC  
> Updated_at: 2016-08-12 23:22:13 UTC  
> Url: https://github.com/boostorg/align/pull/5#issuecomment-239582627  

Merged and pushed.

---
