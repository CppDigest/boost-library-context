# #45 - fatal error: boost/  detail/endian.hpp: No such file or directory | #include <boost/detail/endian.hpp> [Closed]

> Username: paroque28  
> Created at: 2020-07-28 03:00:56 UTC  
> Updated at: 2020-07-28 14:00:35 UTC  
> Closed at: 2020-07-28 14:00:35 UTC  
> Url: https://github.com/boostorg/endian/issues/45  

In 1.73.0 I get the following error:  
  
```  
fatal error: boost/  detail/endian.hpp: No such file or directory | #include <boost/detail/endian.hpp>  
```  
The behavior is different in 1.72.0

---

## Comment 1

> Username: paroque28  
> Created at: 2020-07-28 03:03:22 UTC  
> Updated at: 2020-07-28 03:49:37 UTC  
> Url: https://github.com/boostorg/endian/issues/45#issuecomment-664748298  

I see that in https://github.com/boostorg/endian/commit/3a50c15a9734439598b46442568b375a52c9d63d , `Boost::predef` is missing which doesn't match the commit message: "Add CMake install support, tests "  
  
```  
 -   INTERFACE  
 -       Boost::config  
 -       Boost::core  
 -       Boost::predef  
 -       Boost::static_assert  
 -       Boost::type_traits  
 + INTERFACE  
 +   Boost::config  
 +   Boost::core  
 +   Boost::static_assert  
 +   Boost::type_traits  
```  
  
Was this made on purpose?

---

## Comment 2

> Username: paroque28  
> Created at: 2020-07-28 03:50:26 UTC  
> Updated at: 2020-07-28 03:57:41 UTC  
> Url: https://github.com/boostorg/endian/issues/45#issuecomment-664760174  

Disappeared:  
https://www.boost.org/doc/libs/1_73_0/boost/predef/detail/endian_compat.h  
https://www.boost.org/doc/libs/1_73_0/boost/detail/endian.hpp  
  
Before:  
https://www.boost.org/doc/libs/1_72_0/boost/predef/detail/endian_compat.h  
https://www.boost.org/doc/libs/1_72_0/boost/detail/endian.hpp  
  
Is this the reason:  
```  
The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2020-07-28 12:31:02 UTC  
> Url: https://github.com/boostorg/endian/issues/45#issuecomment-665011461  

Yes, this is the reason. The deprecated headers (part of the Predef library, not the Endian library) were removed in https://github.com/boostorg/predef/commit/aa6e232bf170ad8b856aff9e7c70334f77441c7f.
