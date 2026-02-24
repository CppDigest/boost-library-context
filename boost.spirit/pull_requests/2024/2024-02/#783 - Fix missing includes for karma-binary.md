# #783 Fix missing includes for karma/binary [Merged]

> Username: iv-m  
> Created at: 2024-02-07 15:41:27 UTC  
> Updated at: 2024-02-23 01:58:51 UTC  
> Merged at: 2024-02-07 18:35:05 UTC  
> Closed at: 2024-02-07 18:35:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/783  

Previosuly, scoped_enum was included into karma's binary.hpp through boost/endian/detail/order.hpp. Since Boost 1.84.0 that file stopped using scoped enum and switched to use plain enum class instead; so it needs to be added here.  
  
Fixes #779

---

## Comment 1

> Username: iv-m  
> Created_at: 2024-02-07 15:44:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/783#issuecomment-1932324272  

I discovered this issue when trying to build some code with boost 1.84.0. With boost 1.83.0 it compiled just fine, but with 1.84.0 it fails with  
  
```  
/usr/include/boost/spirit/home/karma/binary/binary.hpp:197:19: error: 'BOOST_SCOPED_ENUM' has not been declared  
  197 |         template <BOOST_SCOPED_ENUM(boost::endian::order) bits>  
      |                   ^~~~~~~~~~~~~~~~~  
```  
  
... and  a few screens of various errors.

---
