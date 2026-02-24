# #134 - Deprecation warning from Boost.Predef in binary_iarchive [Closed]

> Username: jeking3  
> Created at: 2018-10-30 12:22:27 UTC  
> Updated at: 2018-12-20 16:58:20 UTC  
> Closed at: 2018-12-20 16:58:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/134  

```  
gcc.compile.c++ ../../bin.v2/libs/serialization/build/gcc-7.3.0/debug/visibility-hidden/binary_iarchive.o  
In file included from ../../boost/detail/endian.hpp:9:0,  
                 from ../../boost/archive/impl/basic_binary_iarchive.ipp:25,  
                 from ../../libs/serialization/src/binary_iarchive.cpp:20:  
../../boost/predef/detail/endian_compat.h:11:161: note: #pragma message: The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead  
 #pragma message("The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead")  
                                                                                                                                                                 ^  
```
