# #155 - Boost depreciation of BOOST_*_ENDIAN [Closed]

> Username: anadon  
> Created at: 2019-04-29 16:02:47 UTC  
> Updated at: 2019-10-30 18:00:43 UTC  
> Closed at: 2019-10-19 16:34:14 UTC  
> Url: https://github.com/boostorg/serialization/issues/155  

I'm trying other ways to run graph tests, and the following came up.  
  
```  
clang-linux.compile.c++.without-pch ../../../bin.v2/libs/serialization/build/clang-linux-8.0.0/debug/threading-multi/visibility-hidden/binary_iarchive.o  
In file included from ../../../libs/serialization/src/binary_iarchive.cpp:20:  
In file included from ../../../boost/archive/impl/basic_binary_iarchive.ipp:25:  
In file included from ../../../boost/detail/endian.hpp:9:  
../../../boost/predef/detail/endian_compat.h:11:9: warning: The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead [-W#pragma-messages]  
#pragma message("The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead")  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2019-10-19 16:34:14 UTC  
> Url: https://github.com/boostorg/serialization/issues/155#issuecomment-544169299  

hmmm - seems to me a better place to address this would be in boost/detail/endian.hpp or boost/predef/detail/endian_compat.h .  
  
So I'm closing this issue.  Feel free to re-open it if I'm wrong.

---

## Comment 2

> Username: Xanderus  
> Created at: 2019-10-30 16:52:01 UTC  
> Url: https://github.com/boostorg/serialization/issues/155#issuecomment-548005297  

The warning means that including boost/detail/endian.hpp is deprecated.  
archive/impl/basic_binary_iarchive.ipp uses macros BOOST_LITTLE_ENDIAN and BOOST_BIG_ENDIAN; these two should be replaced with BOOST_ENDIAN_*_BYTE, as the message says.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-10-30 18:00:43 UTC  
> Url: https://github.com/boostorg/serialization/issues/155#issuecomment-548038891  

Looks like this has been fixed by https://github.com/boostorg/serialization/pull/125.
