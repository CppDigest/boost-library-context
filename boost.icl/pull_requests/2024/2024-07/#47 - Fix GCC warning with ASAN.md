# #47 Fix GCC warning with ASAN [Merged]

> Username: orgads  
> Created at: 2024-07-10 14:15:31 UTC  
> Updated at: 2025-07-10 17:24:49 UTC  
> Merged at: 2025-07-10 16:52:15 UTC  
> Closed at: 2025-07-10 16:52:15 UTC  
> Url: https://github.com/boostorg/icl/pull/47  

Example:  
```  
boost/icl/concept/interval_associator.hpp:1132:58: error: 'boost::icl::element_iterator<...>::_inter_pos' is used uninitialized [-Werror=uninitialized]  
  1132 |     return typename Type::element_iterator(object.begin());  
       |                                                          ^  
```

---

## Comment 1

> Username: orgads  
> Created_at: 2024-07-19 08:49:35 UTC  
> Url: https://github.com/boostorg/icl/pull/47#issuecomment-2238687075  

ping

---
