# #101 - predef/other/endian.h still uses BOOST_OS_ANDROID [Closed]

> Username: Rohil  
> Created at: 2019-08-29 00:43:57 UTC  
> Updated at: 2020-02-29 15:34:52 UTC  
> Closed at: 2020-02-29 15:34:52 UTC  
> Url: https://github.com/boostorg/predef/issues/101  

BOOST_OS_ANDROID has been deprecated and resolves to 0 on Android as of boost 1.71.   
  
However, this flag continues to be used in Line 57 in [predef/platform/endian.h](https://github.com/boostorg/predef/blob/develop/include/boost/predef/other/endian.h#L57)  
  
It should be replaced with BOOST_PLAT_ANDROID or BOOST_OS_LINUX, as appropriate.
