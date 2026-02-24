# #453 - dll-interface warning [Closed]

> Username: vinniefalco  
> Created at: 2020-10-21 17:20:40 UTC  
> Updated at: 2021-06-23 20:46:29 UTC  
> Closed at: 2021-06-23 20:46:29 UTC  
> Url: https://github.com/boostorg/json/issues/453  

We're getting this now  
> memory_resource.cpp  
C:\Users\vinnie\src\boost\boost/json/detail/default_resource.hpp(30): warning C4251: 'boost::json::detail::default_resource::instance_': union 'boost::json::detail::default_resource::holder' needs to have dll-interface to be used by clients of class 'boost::json::detail::default_resource'  
C:\Users\vinnie\src\boost\boost/json/detail/default_resource.hpp(26): note: see declaration of 'boost::json::detail::default_resource::holder'  
C:\Users\vinnie\src\boost\boost/json/monotonic_resource.hpp(95): warning C4251: 'boost::json::monotonic_resource::upstream_': class 'boost::json::storage_ptr' needs to have dll-interface to be used by clients of class 'boost::json::monotonic_resource'  
C:\Users\vinnie\src\boost\boost/json/storage_ptr.hpp(83): note: see declaration of 'boost::json::storage_ptr'

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-06-23 07:48:22 UTC  
> Url: https://github.com/boostorg/json/issues/453#issuecomment-866612089  

This seems to have been fixed  
https://github.com/boostorg/json/blob/develop/include/boost/json/detail/default_resource.hpp#L21  
https://github.com/boostorg/json/blob/develop/include/boost/json/monotonic_resource.hpp#L24
