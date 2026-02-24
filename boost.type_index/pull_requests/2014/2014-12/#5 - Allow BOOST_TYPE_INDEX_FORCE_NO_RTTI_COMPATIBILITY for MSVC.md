# #5 Allow BOOST_TYPE_INDEX_FORCE_NO_RTTI_COMPATIBILITY for MSVC [Merged]

> Username: MarcelRaad  
> Created at: 2014-12-08 17:31:24 UTC  
> Updated at: 2014-12-17 22:54:12 UTC  
> Merged at: 2014-12-17 20:04:38 UTC  
> Closed at: 2014-12-17 20:04:38 UTC  
> Url: https://github.com/boostorg/type_index/pull/5  

BOOST_TYPE_INDEX_FORCE_NO_RTTI_COMPATIBILITY had no effect for MSVC. With this change, it is treated like BOOST_NO_RTTI also for MSVC.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2014-12-17 20:05:53 UTC  
> Url: https://github.com/boostorg/type_index/pull/5#issuecomment-67384390  

Thanks for finding this issue and providing the fix!

---
