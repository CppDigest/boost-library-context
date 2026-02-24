# #100 Fix removed typedef in std::allocator in C++20 [Closed]

> Username: stac47  
> Created at: 2020-02-17 08:46:43 UTC  
> Updated at: 2020-03-06 10:00:53 UTC  
> Closed at: 2020-02-17 12:55:48 UTC  
> Url: https://github.com/boostorg/log/pull/100  

In C++ 20, lots of members of std::allocator were removed: https://en.cppreference.com/w/cpp/memory/allocator  
```  
libs/log/src/attribute_set_impl.hpp:68:41: error: no type named ‘pointer’ in ‘class std::allocator<boost::log::v2_mt_posix::attribute_set::node>’                                                                                                                                           68 |     typedef typename base_type::pointer pointer;                                                                                                                                                                                                                                       |                                         ^~~~~~~  
libs/log/src/attribute_set_impl.hpp:69:47: error: no type named ‘const_pointer’ in ‘class std::allocator<boost::log::v2_mt_posix::attribute_set::node>’  
   69 |     typedef typename base_type::const_pointer const_pointer;  
      |                                               ^~~~~~~~~~~~~  
libs/log/src/attribute_set_impl.hpp:70:43: error: no type named ‘reference’ in ‘class std::allocator<boost::log::v2_mt_posix::attribute_set::node>’  
   70 |     typedef typename base_type::reference reference;  
      |                                           ^~~~~~~~~  
libs/log/src/attribute_set_impl.hpp:71:49: error: no type named ‘const_reference’ in ‘class std::allocator<boost::log::v2_mt_posix::attribute_set::node>’  
   71 |     typedef typename base_type::const_reference const_reference;  
      |                                                 ^~~~~~~~~~~~~~~  
```  
This PR fixes these compilation issues.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-02-17 12:57:12 UTC  
> Url: https://github.com/boostorg/log/pull/100#issuecomment-586981275  

Thanks for the report.

---
