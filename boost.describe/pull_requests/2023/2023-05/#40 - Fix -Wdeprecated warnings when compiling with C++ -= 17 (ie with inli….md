# #40 Fix -Wdeprecated warnings when compiling with C++ >= 17 (ie with inli… [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2023-05-20 23:47:00 UTC  
> Updated at: 2023-05-21 02:54:47 UTC  
> Merged at: 2023-05-21 02:54:47 UTC  
> Closed at: 2023-05-21 02:54:47 UTC  
> Url: https://github.com/boostorg/describe/pull/40  

…ne variables).  
  
Example of warning we get:  
```  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/describe/members.hpp:89:81: error: redundant redeclaration of 'constexpr' static data member 'boost::describe::detail::update_modifiers<T, Bm>::fn<D>::pointer' [-Werror=deprecated]  
 89 | template<class T, unsigned Bm> template<class D> constexpr decltype(D::pointer) update_modifiers<T, Bm>::fn<D>::pointer;  
    | ^~~~~~~~~~~~~~~~~~~~~~~ /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/describe/members.hpp:83:47: note: previous declaration of 'boost::describe::detail::update_modifiers<T, Bm>::fn<D>::pointer'  
 83 | static constexpr decltype(D::pointer) pointer = D::pointer;  
    | ^~~~~~~  
```

---
