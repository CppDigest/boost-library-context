# #14 BOOST_NO_CXX11_ALLOCATOR should be defined with libc++ in C++03 mode [Merged]

> Username: glenfe  
> Created at: 2014-05-08 20:24:42 UTC  
> Updated at: 2014-05-09 02:06:51 UTC  
> Merged at: 2014-05-09 02:06:51 UTC  
> Closed at: 2014-05-09 02:06:51 UTC  
> Url: https://github.com/boostorg/config/pull/14  

BOOST_NO_CXX11_ALLOCATOR should imply no support for the C++11 allocator model. The C++11 allocator model requires a conforming std::allocator_traits which is only possible with C++11 template aliases since members rebind_alloc and rebind_traits require it.

---
