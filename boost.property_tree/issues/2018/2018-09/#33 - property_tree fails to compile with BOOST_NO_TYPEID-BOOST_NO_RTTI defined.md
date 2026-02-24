# #33 - property_tree fails to compile with BOOST_NO_TYPEID/BOOST_NO_RTTI defined [Closed]

> Username: clevijoki  
> Created at: 2018-09-13 19:35:30 UTC  
> Updated at: 2023-11-14 14:00:32 UTC  
> Closed at: 2023-11-14 14:00:32 UTC  
> Url: https://github.com/boostorg/property_tree/issues/33  

typeid() is used in some exception macros in detail/ptree_implementation.hpp

---

## Comment 1

> Username: andreasschultes  
> Created at: 2019-03-13 23:17:15 UTC  
> Url: https://github.com/boostorg/property_tree/issues/33#issuecomment-472643212  

https://github.com/boostorg/property_tree/pull/20  
https://github.com/boostorg/property_tree/pull/21

---

## Comment 2

> Username: ashtum  
> Created at: 2023-11-14 13:59:38 UTC  
> Url: https://github.com/boostorg/property_tree/issues/33#issuecomment-1810256632  

Addressed in https://github.com/boostorg/property_tree/commit/3b7c3ccb6bf5438c1a2d1b663762d69760a6386d.
